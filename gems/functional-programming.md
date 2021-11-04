# Functional programming 函数式编程

D强调*函数式编程*并为函数式风格的开发提供一流的支持。

在D中，可以将函数声明为`纯pure`，这意味着在相同的输入参数的情况下，总是生成**相同的**输出。 `纯`函数不能访问或改变任何可变的全局状态，因此只允许调用其他本身是`纯`的函数。

    int add(int lhs, int rhs) pure {
        // 错误：非纯函数
        return lhs + rhs;
    }

这种`add`的变体称为**强纯函数**，因为它返回的结果只依赖于其输入参数而不修改它们。 D还允许定义可以有可变参数的**弱纯函数**：

    void add(ref int result, int lhs, int rhs) pure {
        result = lhs + rhs;
    }

这些函数仍然被认为是纯的，不能访问或更改任何可变的全局状态，只能改变传入的可变参数。

由于`纯`的限制，纯函数在多线程环境是非常理想的，在*设计上*就防止了数据竞争。 另外，纯函数可以更轻易地进行缓存，并允许一系列编译器优化。

The attribute `pure` is automatically inferred
by the compiler for templated functions and `auto` functions,
where applicable (this is also true for `@safe`, `nothrow`,
and `@nogc`).
对于模板函数和`auto`函数，编译器会根据适用与否自动推断属性是否是`纯`。（`@safe`，`nothrow`和`@nogc`也是如此）

### In-depth 深入

- [Functional DLang Garden](https://garden.dlang.io/)

## {SourceCode}

```d
import std.bigint : BigInt;

/**
 * Computes the power of a base
 * with an exponent.
 *
 * Returns:
 *     Result of the power as an
 *     arbitrary-sized integer
 */
BigInt bigPow(uint base, uint power) pure
{
    BigInt result = 1;

    foreach (_; 0 .. power)
        result *= base;

    return result;
}

void main()
{
    import std.datetime : benchmark, to;
    import std.functional : memoize;
    import std.stdio : writefln, writeln;

    // memoize caches the result of the function
    // call depending on the input parameters.
    // pure functions are great for that!
    alias fastBigPow = memoize!(bigPow);

    void test()
    {
        writefln(".uintLength() = %s ",
        	   fastBigPow(5, 10000).uintLength);
    }

    foreach (i; 0 .. 10)
        benchmark!test(1)[0]
        	.to!("msecs", double)
        	.writeln("took: miliseconds");
}
```
