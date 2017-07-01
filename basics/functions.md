# 函数

我们已经介绍了过了一个函数函数：`main()`，它是每一个 D 程序的入口。
每个函数都可以返回一个值（如果函数不需要返回值，那么可以将它的返回值类型声明为 `void`）。

```d
    int add(int lhs, int rhs) {
        return lhs + rhs;
    }
```

### `auto` 返回值类型

如果函数的返回值类型被定义为 `auto`，那么 D 编译器会自动推断函数的返回值类型。 因此，在 `auto` 返回值类型的函数里，如果包含了多条 `return` 语句，那么它们返回的值必须有着兼容的类型。

```d
    auto add(int lhs, int rhs) { // 返回值类型为 int
        return lhs + rhs;
    }

    auto lessOrEqual(int lhs, int rhs) { // 返回值类型为 `double`
        if (lhs <= rhs)
            return 0;
        else
            return 1.0;
    }
```

### 默认参数

函数支持可选的为参数提供默认值，这避免了冗长的重载。

```d
    void plot(string msg, string color = "red") {
        //...
    }
    plot("D rocks");
    plot("D rocks", "blue");
```

如果指定了一个参数的默认值，那么他后面的所有参数都必须拥有默认值。

### 局部函数

函数甚至能够定义在其他函数内部，它们可以在局部被使用，并且对于外部是不可见的。这样的局部函数甚至能够访问局部变量：

```d
    void fun() {
        int local = 10;
        int fun_secret() {
            local++; // 合法
        }
        // ...
```

这样的嵌套函数被称为 *委托*，在[这里](basics/delegates)我们会有详细的讲解。

### In-depth

- [Functions in _Programming in D_](http://ddili.org/ders/d.en/functions.html)
- [Function parameters in _Programming in D_](http://ddili.org/ders/d.en/function_parameters.html)
- [Function specification](https://dlang.org/spec/function.html)

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
