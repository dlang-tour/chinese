# 函数

我们前面介绍了`main()`函数，它是每一个D程序的起始点。一个函数可以返回一个值（或通过定义为`void`不返回任何东西）并接受任意数量的参数：

    int add(int lhs, int rhs) {
        return lhs + rhs;
    }

### `auto` 返回值类型

如果返回类型被定义为 `auto`, D编译器将自动推断返回值类型。 因此多个`return`表达式返回的类型必须兼容。

    auto add(int lhs, int rhs) { // 返回 `int`
        return lhs + rhs;
    }

    auto lessOrEqual(int lhs, int rhs) { // 返回 `double`
        if (lhs <= rhs)
            return 0;
        else
            return 1.0;
    }

### 默认参数

函数可以选择性地定义默认参数，这让我们不必为声明太多的函数重载而殚精竭虑。

    void plot(string msg, string color = "red") {
        ...
    }
    plot("D rocks");
    plot("D rocks", "blue");

一旦指定了一个默认参数，所有往后的参数都必须是默认参数了。.

### 局部函数

函数甚至可以在其他函数中进行声明，它们可以在局部作用域中使用，但在外部作用域不可见。这些函数可以存取其父作用域的局部对象：

    void fun() {
        int local = 10;
        int fun_secret() {
            local++; // 这是合法的
        }
        ...

这样的嵌套函数被称作委托（delegates）, 我们在[后面](basics/delegates)会深入地去解释这个概念。

### In-depth

- [_D程序设计_ 中的函数](http://ddili.org/ders/d.en/functions.html)
- [_D程序设计_ 中的函数参数](http://ddili.org/ders/d.en/function_parameters.html)
- [函数规范](https://dlang.org/spec/function.html)

## {SourceCode}

```d
import std.stdio : writeln;
import std.random : uniform;

void randomCalculator()
{
    //  为四则运算定义4个局部函数
    auto add(int lhs, int rhs) {
        return lhs + rhs;
    }
    auto sub(int lhs, int rhs) {
        return lhs - rhs;
    }
    auto mul(int lhs, int rhs) {
        return lhs * rhs;
    }
    auto div(int lhs, int rhs) {
        return lhs / rhs;
    }

    int a = 10;
    int b = 5;

    // uniform 生成一个在START和END之间，但不包括END的数字。
    // 根据结果，我们调用其中一个数学函数。
    switch (uniform(0, 4)) {
        case 0:
            writeln(add(a, b));
            break;
        case 1:
            writeln(sub(a, b));
            break;
        case 2:
            writeln(mul(a, b));
            break;
        case 3:
            writeln(div(a, b));
            break;
        default:
            // 标识无法到达的代码的特殊代码
            assert(0);
    }
}

void main()
{
    randomCalculator();
    // add(), sub(), mul() 和 div()
    // 都对外部环境不可见
    static assert(!__traits(compiles,
                            add(1, 2)));
}

```
