# 可变性

D 是静态类型的语言：变量在声明之后，类型就不能再发生变化。这样能让编译器尽早发现错误，并且在编译时强制进行限制。良好的类型安全性使得大型项目更容易维护。

D 中拥有很多类型修饰符，但是最常用的还是 `const` 和 `immutable`。

### `immutable`

除了静态的类型系统，D 还提供了类型修饰符（有时也成为“类型构造函数”），它们允许对某些变量附加约束。例如，`immutable` 的变量只能够被初始化一次，之后不允许再修改：

```d
    immutable int err = 5;
    // 或者： immutable err = 5 ，err 的类型 int 会被自动推断出来
    err = 5; // 编译错误
```

`immutable` objects can thus be safely shared among different threads with no
synchronization because they never change by definition. This also implies that
`immutable` objects can be cached perfectly.

### `const`

`const` objects can't be modified, too. This restriction is just valid for the
current scope. A `const` pointer can be created from either a *mutable* or
`immutable` object. This means that the object is `const` for your current
scope, but someone else might modify it from a different context. It is common
for APIs to accept `const` arguments to ensure they don't modify the input as
that allows the same function to process both mutable and immutable data.

    void foo(const char[] s)
    {
        // if not commented out, next line will
        // result in error (can't modify const):
        // s[0] = 'x';

        import std.stdio : writeln;
        writeln(s);
    }

    // thanks to `const`, both calls will compile:
    foo("abcd"); // string is an immutable array
    foo("abcd".dup); // .dup returns a mutable copy

Both `immutable` and `const` are _transitive_ type qualifiers, which ensures that once
`const` is applied to a type, it applies recursively to every sub-component of that type.

### In-depth

#### Basic references

- [Immutable in _Programming in D_](http://ddili.org/ders/d.en/const_and_immutable.html)
- [Scopes in _Programming in D_](http://ddili.org/ders/d.en/name_space.html)

#### Advanced references

- [const(FAQ)](https://dlang.org/const-faq.html)
- [Type qualifiers in D](https://dlang.org/spec/const3.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    /**
    * Variables are mutable by default and
    * editing them is allowed:
    */
    int m = 100; // mutable
    writeln("m: ", typeof(m).stringof);
    m = 10; // fine

    /**
    * Pointing to mutable memory:
    */
    // A const pointer to mutable memory is
    // allowed
    const int* cm = &m;
    writeln("cm: ", typeof(cm).stringof);
    // By defintion `const` can't be modified:
    // *cm = 100; // error!

    // As `immutable` is guaranteed to stay
    // unchanged, it can't point to
    // mutable memory
    // immutable int* im = &m; // error!

    /**
    * Pointing to readonly memory:
    */
    immutable v = 100;
    writeln("v: ", typeof(v).stringof);
    // v = 5; // error!

    // `const` may point to readonly memory,
    // but it is readonly as well
    const int* cv = &v;
    writeln("*cv: ", typeof(cv).stringof);
    // *cv = 10; // error!
}
```
