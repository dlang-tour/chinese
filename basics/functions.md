# 函数

已经引入了一个函数: `main()` - 这是每个 D 程序的起点。
一个函数可以返回一个值(如果没有返回值，则用`void`声明)，并接受任意数量的参数 :

    int add(int lhs, int rhs) {
        return lhs + rhs;
    }

### `auto` 返回值类型

如果返回类型定义为 `auto`, 则 D 编译器会自动推断返回类型。
因此，多个 `return` 语句必须返回具有兼容类型的值。

    auto add(int lhs, int rhs) { // 返回 `int`类型的数据
        return lhs + rhs;
    }

    auto lessOrEqual(int lhs, int rhs) { // 返回 `double`类型的数据
        if (lhs <= rhs)
            return 0;
        else
            return 1.0;
    }

### 默认参数

函数可以选择定义默认参数。
这避免了声明冗余重载的繁琐工作。

    void plot(string msg, string color = "red") {
        ...
    }
    plot("D rocks");
    plot("D rocks", "blue");

一旦指定了默认参数，下面所有的参数也必须是默认参数。

### 局部函数

函数甚至可以在其他函数中声明，在其他函数中声明的话，它们可以在局部使用，对外部是不可使用的。
这些函数甚至可以访问父作用域的局部对象:

    void fun() {
        int local = 10;
        int fun_secret() {
            local++; // that's legal
        }
        ...

这种嵌套函数称为委托，我们将更深入地解释它们。
[soon](basics/delegates).

### 深入

-   [Functions in _Programming in D_](http://ddili.org/ders/d.en/functions.html)
-   [Function parameters in _Programming in D_](http://ddili.org/ders/d.en/function_parameters.html)
-   [Function specification](https://dlang.org/spec/function.html)

## {SourceCode}

```d
import std.stdio : writeln;
import std.random : uniform;

void randomCalculator()
{
    // Define 4 local functions for
    // 4 different mathematical operations
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

    // uniform generates a number between START
    // and END, whereas END is NOT inclusive.
    // Depending on the result we call one of
    // the math operations.
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
            // special code which marks
            // UNREACHABLE code
            assert(0);
    }
}

void main()
{
    randomCalculator();
    // add(), sub(), mul() and div()
    // are NOT visible outside of their scope
    static assert(!__traits(compiles,
                            add(1, 2)));
}

```
