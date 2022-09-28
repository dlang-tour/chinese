# Delegates 委托

### 函数作为参数

在D中，函数也可以作为一个函数的参数：

    void doSomething(int function(int, int) doer) {
        // 调用参数传过来的函数
        doer(5,5);
    }

    doSomething(add); // 用全局的 `add` 函数作为参数
                      // add 函数需要两个参数

`doer` 可以像其他函数调用那样调用。

### Local functions with context 带有上下文的本地函数

上面的例子使用 `function` 类型，它其实就是一个独立的函数指针。下面，我们要调用成员函数或本地函数，那么就要使用委托（`delegate`）了 . 它也是个函数指针，但是带着上下文（或者叫 _enclosure_ ），在其他语言中也叫做 *closure 闭包*。例如，对于类的成员函数，委托（`delegate`） 既包含在这个函数的指针，也包含当前对象的引用（指针）。在函数内创建的委托 （`delegate`），包含当前函数的上下文，D 编译器会把委托需要的上下文复制到堆中保证内存安全，并在委托里引用这块内存区域。 

    void foo() {
        void local() {
            writeln("local");
        }
        auto f = &local; // f 是 delegate() 类型
    }

同样的 `doSomething` 用委托（`delegate`）做参数，这样声明：

    void doSomething(int delegate(int,int) doer);

`delegate` 和 `function` 不能混用. 标准库中有提供
[`std.functional.toDelegate`](https://dlang.org/phobos/std_functional.html#.toDelegate)
 去转换一个 `function` 到 `delegate`.

### 匿名函数和 & Lambdas 表达式

函数可以当作变量和传递给其他函数，对于每个函数起名就比较困难，所以 D 允许匿名函数和单行的 _lambdas_ 。

    auto f = (int lhs, int rhs) {
        return lhs + rhs;
    };
    auto f = (int lhs, int rhs) => lhs + rhs; // Lambda - 内部会自动转换成上面那样

在标准库中，也可以把字符串当作函数去传给模板。
例如，这个例子使用叠加（reducer）：

    [1, 2, 3].reduce!`a + b`; // 6

字符串函数只支持 _一个_ 和 _两个__ 参数的函数，使用字符 `a`
作为第一个参数，使用字符 `b` 作为第二个参数.

### 深入了解

- [Delegate specification](https://dlang.org/spec/function.html#closures)

## {SourceCode}

```d
import std.stdio : writeln;

enum IntOps {
    add = 0,
    sub = 1,
    mul = 2,
    div = 3
}

/**
提供数学计算
参数:
    op = 运算操作符
返回值: 执行数学计算的函数
*/
auto getMathOperation(IntOps op)
{
    // 定一个4个lambda 表达式去处理四种数学运算
    auto add = (int lhs, int rhs) => lhs + rhs;
    auto sub = (int lhs, int rhs) => lhs - rhs;
    auto mul = (int lhs, int rhs) => lhs * rhs;
    auto div = (int lhs, int rhs) => lhs / rhs;

    // 确保所有的的分支都被处理
    final switch (op) {
        case IntOps.add:
            return add;
        case IntOps.sub:
            return sub;
        case IntOps.mul:
            return mul;
        case IntOps.div:
            return div;
    }
}

void main()
{
    int a = 10;
    int b = 5;

    auto func = getMathOperation(IntOps.add);
    writeln("The type of func is ",
        typeof(func).stringof, "!");

    // 运行处理 delegate 函数
    writeln("result: ", func(a, b));
}
```
