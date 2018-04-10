# Traits

D的强大之处之一是其编译时功能评估（compile-time function evaluation， CTFE）系统。 结合introspection，可以编写泛型程序并实现高度优化。

## 显式关联 Explicit contracts

Traits允许明确指定接受哪些输入。例如`splitIntoWords`可以在任意字符串类型上进行操作：

```d
S[] splitIntoWord(S)(S input)
if (isSomeString!S)
```

这也适用于模板参数，`myWrapper`可以确保传入的符号是可调用的函数：

```d
void myWrapper(alias f)
if (isCallable!f)
```

作为一个简单的例子，我们可以分析`std.algorithm.searching`中返回两个range公共前缀的[`commonPrefix`]（https://dlang.org/phobos/std_algorithm_searching.html#.commonPrefix）：

```d
auto commonPrefix(alias pred = "a == b", R1, R2)(R1 r1, R2 r2)
if (isForwardRange!R1
    isInputRange!R2 &&
    is(typeof(binaryFun!pred(r1.front, r2.front)))) &&
    !isNarrowString!R1)
```

这意味着该函数只有在下列情况时才能编译并调用：

- `r1`是可保存的（由`isForwardRange`保证）
- `r2`是可迭代的（由`isInputRange`保证）
- `r1`和`r2`的元素类型，`pred`可以调用
- `r1`不是一个窄字符串（`char []`，`string`，`wchar`或`wstring`） - 为了简单性，否则可能需要解码

### Specialization

许多API希望成为通用的，所以他们不希望为这个一般化付出额外的运行时间。<!-- 不知道怎么翻 -->
凭借introspection和CTFE的强大功能，可以在编译时指定一种在给定输入类型的情况下达到最佳性能的方法。

一个常见的问题是，与数组相比，您在遍历它之前不知道流或列表的确切长度。
因此，为任意可迭代类型推广的`std.range`方法`walkLength`的简单实现将是：

```d
static if (hasMember!(r, "length"))
    return r.length; // O(1)
else
    return r.walkLength; // O(n)
```

#### `commonPrefix`

编译时introspection的使用在Phobos中无处不在。 例如`commonPrefix`不同于`RandomAccessRange`和线性可迭代range，因为在`RandomAccessRange`中可以在不同位置之间跳转，从而加快算法的速度。

#### More CTFE magic

[std.traits](https://dlang.org/phobos/std_traits.html) wraps most of
D's [traits](https://dlang.org/spec/traits.html) except for some like
`compiles` that can't be wrapped as it would lead to an immediate compile error:

```d
__traits(compiles, obvious error - $%42); // false
```

#### Special keywords

Additionally for debugging purposes D provides a couple of special keywords:

```d
void test(string file = __FILE__, size_t line = __LINE__, string mod = __MODULE__,
          string func = __FUNCTION__, string pretty = __PRETTY_FUNCTION__)
{
    writefln("file: '%s', line: '%s', module: '%s',\nfunction: '%s', pretty function: '%s'",
             file, line, mod, func, pretty);
}
```

With D's CLI evaluation one doesn't even need `time` - CTFE can be used!

```d
rdmd --force --eval='pragma(msg, __TIMESTAMP__);'
```

## In-depth

- [std.range.primitives](https://dlang.org/phobos/std_range_primitives.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [std.meta](https://dlang.org/phobos/std_meta.html)
- [Specification on Traits in D](https://dlang.org/spec/traits.html)

## {SourceCode}

```d
import std.functional : binaryFun;
import std.range.primitives : empty, front,
    popFront,
    isInputRange,
    isForwardRange,
    isRandomAccessRange,
    hasSlicing,
    hasLength;
import std.stdio : writeln;
import std.traits : isNarrowString;

/**
Returns the common prefix of two ranges
without the auto-decoding special case.

Params:
    pred = Predicate for commonality comparison
    r1 = A forward range of elements.
    r2 = An input range of elements.

Returns:
A slice of r1 which contains the characters
that both ranges start with.
 */
auto commonPrefix(alias pred = "a == b", R1, R2)
                 (R1 r1, R2 r2)
if (isForwardRange!R1 && isInputRange!R2 &&
    !isNarrowString!R1 &&
    is(typeof(binaryFun!pred(r1.front,
                             r2.front))))
{
    import std.algorithm.comparison : min;
    static if (isRandomAccessRange!R1 &&
               isRandomAccessRange!R2 &&
               hasLength!R1 && hasLength!R2 &&
               hasSlicing!R1)
    {
        immutable limit = min(r1.length,
                              r2.length);
        foreach (i; 0 .. limit)
        {
            if (!binaryFun!pred(r1[i], r2[i]))
            {
                return r1[0 .. i];
            }
        }
        return r1[0 .. limit];
    }
    else
    {
        import std.range : takeExactly;
        auto result = r1.save;
        size_t i = 0;
        for (;
             !r1.empty && !r2.empty &&
             binaryFun!pred(r1.front, r2.front);
             ++i, r1.popFront(), r2.popFront())
        {}
        return takeExactly(result, i);
    }
}

void main()
{
    // prints: "hello, "
    writeln(commonPrefix("hello, world"d,
                         "hello, there"d));
}
```
