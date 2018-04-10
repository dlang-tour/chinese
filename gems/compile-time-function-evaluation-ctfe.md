# Compile Time Function Evaluation (CTFE) 编译时函数求值

CTFE是一种允许编译器在**编译时**执行函数的机制。 这个特性不需要特殊的D语言集 - 只要函数只依赖于编译时已知的值，D编译器就会决定在编译期间解释它。

    // 结果将在编译时计算。
    // 查看机器码，
    // 它不会包含函数调用！
    static val = sqrt(50);

像`static`，`immutable`或`enum`这样的关键字指示编译器尽可能使用CTFE。这种技术的好处是，函数不需要重写就可以使用它，并且相同的代码可以被完美地共享：

    int n = doSomeRuntimeStuff();
    // 与上面相同的函数，但这次它只是在经典的运行时调用。
    auto val = sqrt(n);


D中的一个显然的例子是[std.rege](https://dlang.org/phobos/std_regex.html)库。 它提供使用* string mixins *和CTFE来生成在编译期间生成的高度优化的正则表达式自动机，`ctRegex`类型。 相同的代码被编译允许仅在运行时可用的正则表达式版本`regex` 重新使用。

    auto ctr = ctRegex!(`^.*/([^/]+)/?$`);
    auto tr = regex(`^.*/([^/]+)/?$`);
    // ctr和tr可以互换使用
    // 但是ctr更快！

在CTFE中，并非所有语言特性都是可用的，但支持的功能随编译器的版本增加而增加。

### In-depth 深入

- [Introduction to regular expressions in D](https://dlang.org/regular-expression.html)
- [std.regex](https://dlang.org/phobos/std_regex.html)
- [Conditional compilation](https://dlang.org/spec/version.html)

## {SourceCode}

```d
import std.stdio : writeln;

/**
使用牛顿迭代法计算数字的平方根。

参数:
    x = 将要求平方根的数字
    
返回值: x 的平方根
*/
auto sqrt(T)(T x) {
    // our epsilon when to stop the
    // approximation because we think the change
    // isn't worth another iteration.
    enum GoodEnough = 0.01;
    import std.math : abs;
    // choose a good starting value.
    T z = x*x, old = 0;
    int iter;
    while (abs(z - old) > GoodEnough) {
        old = z;
        z -= ((z*z)-x) / (2*z);
    }

    return z;
}

void main() {
    double n = 4.0;
    writeln("The sqrt of runtime 4 = ",
        sqrt(n));
    static cn = sqrt(4.0);
    writeln("The sqrt of compile time 4 = ",
        cn);
}
```
