# Ranges 范围

一个 `foreach` 被编译器处理后

```
foreach (element; range)
{
    // 循环体...
}
```

内部将重写成类似于以下内容：

```
for (auto __rangeCopy = range;
        !__rangeCopy.empty;
        __rangeCopy.popFront())
 {
    auto element = __rangeCopy.front;
    // 循环体...
}
```
任何满足以下接口的对象被称为 **range 范围**（或者具体称为 `InputRange`），它是一个可以迭代的类型：

```
interface InputRange(E)
{
    bool empty();
    E front();
    void popFront();
}
```

查看右边的例子，InputRange 的具体实现和使用。

## Laziness 惰性求值

范围是 __惰性__ 的。非必要不会对他们进行计算。所以可以对范围取无穷值：

```d
42.repeat.take(3).writeln; // [42, 42, 42]
```

## 值 vs 引用

如果范围的内容是值类型，则范围会复制并且只使用副本：

```d
auto r = 5.iota;
r.drop(5).writeln; // []
r.writeln; // [0, 1, 2, 3, 4]
```

如果范围的内容是引用类型（比如：`class` 或者 [`std.range.refRange`](https://dlang.org/phobos/std_range.html#refRange)），则范围会被消费掉并且不会重置：

```d
auto r = 5.iota;
auto r2 = refRange(&r);
r2.drop(5).writeln; // []
r2.writeln; // []
```

### 可复制的 `InputRanges` 范围 `ForwardRanges`

大部分标准库的范围都是结构体，所以每次 `foreach` 通常都是带破坏性的，所以是没有保障的。如果这个保障很重要的话，`InputRange` 可以专门化为 **forward 转发** 范围，包含一个 `.save` 方法：

```
interface ForwardRange(E) : InputRange!E
{
    typeof(this) save();
}
```

```d
// by value (Structs)
auto r = 5.iota;
auto r2 = refRange(&r);
r2.save.drop(5).writeln; // []
r2.writeln; // [0, 1, 2, 3, 4]
```

### `ForwardRanges` 可以被扩展到 Bidirectional 双向 + random access 随机访问范围

可复制的 `ForwardRange` 有两种扩展：双向范围、随机访问范围。

双向范围允许从后面迭代：

```d
interface BidirectionalRange(E) : ForwardRange!E
{
     E back();
     void popBack();
}
```

```d
5.iota.retro.writeln; // [4, 3, 2, 1, 0]
```

随机访问范围有一个已知的 `length` 长度，并且每一个元素都可以直接访问：

```d
interface RandomAccessRange(E) : ForwardRange!E
{
     E opIndex(size_t i);
     size_t length();
}
```

D 的数组实际上就是随机访问范围：

```d
auto r = [4, 5, 6];
r[1].writeln; // 5
```

### 惰性范围算法

[`std.range`](http://dlang.org/phobos/std_range.html) 和
[`std.algorithm`](http://dlang.org/phobos/std_algorithm.html) 提供了该接口的函数。范围允许在对象后自由组合复杂的迭代算法。此外，范围允许创建 **惰性** 对象，只在迭代真正需要时计算，例如：当下一个范围元素被访问时。特殊的范围算法将在后面的[D's Gems](gems/range-algorithms) 章节中呈现。

### 深入了解

- [`std.algorithm`](http://dlang.org/phobos/std_algorithm.html)
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode}

```d
import std.stdio : writeln;

// 斐波那契
struct FibonacciRange
{
    // 斐波那契状态生成器
    int a = 1, b = 1;

    // 斐波那契不会结束
    enum empty = false;

    // 获取第一个斐波那契数
    int front() const @property
    {
        return a;
    }

    // 删除第一个斐波那契数
    void popFront()
    {
        auto t = a;
        a = b;
        b = t + b;
    }
}

void main()
{
    FibonacciRange fib;

    import std.range : drop, take;
    import std.algorithm.iteration :
        filter, sum;

    // 获取前 10 个斐波那契数
    auto fib10 = fib.take(10);
    writeln("Fib 10: ", fib10);

    // 去除前 5 个
    auto fib5 = fib10.drop(5);
    writeln("Fib 5: ", fib5);

    // 选择偶数子集
    auto even = fib5.filter!(x => x % 2 == 0);
    writeln("FibEven : ", even);

    // 求和
    writeln("Sum of FibEven: ", even.sum);

    // 通常将其结合使用：
    fib.take(10)
         .drop(5)
         .filter!(x => x % 2 == 0)
         .sum
         .writeln;
}
```
