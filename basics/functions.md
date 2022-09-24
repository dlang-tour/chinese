# Functions 函数
已经介绍过的 `main()` 函数是每个 D 程序的起点。一个函数可以有返回值（或没有返回值 `void` ），并且可以接受任意数量的参数：

    // 接受两个 int 类型参数，返回 int 类型值的函数
    int add(int lhs, int rhs) {
        return lhs + rhs;
    }

### 返回 `auto` 类型
如果返回值的类型定义为 `auto` ，D 编译器会自动推断返回值类型。所以，如果函数中有多个 `return` 的话这些返回值需要返回相同或兼容的类型。

    auto add(int lhs, int rhs) { // 返回 `int` 类型
        return lhs + rhs;
    }

    auto lessOrEqual(int lhs, int rhs) { // 返回 `double` 类型
        if (lhs <= rhs)
            return 0;
        else
            return 1.0;
    }

### 默认参数
函数可以选择定义默认参数，避免一些无聊冗余的重载工作。

    void plot(string msg, string color = "red") {
        ...
    }
    plot("D rocks");
    plot("D rocks", "blue");

一旦指定了默认参数，接下来的所有参数也必须是默认参数。

### 局部函数
函数甚至可以在其他函数内部声明，在局部使用，对外部世界不可见。这些函数可以访问父作用域的局部对象：

    void fun() {
        int local = 10;
        int fun_secret() {
            local++; // 合法访问
        }
        ...

这样的嵌套函数称为委托（delegates），稍后将对其进行更深入的[解释](basics/delegates)。

### 深入了解

- [ _D程序设计_ 函数](http://ddili.org/ders/d.en/functions.html)
- [ _D程序设计_ 函数参数](http://ddili.org/ders/d.en/function_parameters.html)
- [Function specification](https://dlang.org/spec/function.html)

## {SourceCode}

```d
import std.stdio : writeln;
import std.random : uniform;

void randomCalculator()
{
    // 为四个不同的数学运算
    // 定义四个局部函数
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

    // 生成包含在开始和结束范围中的数字
    // 不包括结束，
    // 根据结果调用数学运算函数
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
            // 特殊标记不可达的代码
            assert(0);
    }
}

void main()
{
    randomCalculator();
    // add(), sub(), mul() and div()
    // 这几个函数在他们的作用域外是看不到的
    static assert(!__traits(compiles,
                            add(1, 2)));
}

```
