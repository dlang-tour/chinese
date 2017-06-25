# 控制流

程序可以通过 `if` `else` 语句来根据条件控制程序流程：

```d
    if (a == 5) {
        writeln("Condition is met");
    } else if (a > 10) {
        writeln("Another condition is met");
    } else {
        writeln("Nothing is met!");
    }
```

当 `if` 或者 `else` 子句后的代码块只包含一条语句时，可以省略括号。

D 提供了与 C/C++ 以及 Java 相同的操作符来比较对象：

* `==` 和 `!=` 操作符判断对象是否相等或不相等
* `<`, `<=`, `>` 以及 `>=` 操作符判断一个对象是否小于（或等于），大于（或等于）另一个对象

For combining multiple conditions, the `||` operator represents
the logical *OR*, and `&&` the logical *AND*.

D also defines a `switch`..`case` statement which executes one case
depending on the value of *one* variable. `switch`
works with all basic types as well as strings!
It's even possible to define ranges for integral types
using the `case START: .. case END:` syntax. Make sure to
take a look at the source code example.

### 深入

#### 基本参考

- [_D程序设计中的_ 逻辑表达式](http://ddili.org/ders/d.en/logical_expressions.html)
- [_D程序设计中的_ `if` 语句](http://ddili.org/ders/d.en/if.html)
- [_D程序设计中的_ 三元表达式](http://ddili.org/ders/d.en/ternary.html)
- [_D程序设计中的_ `switch` 和 `case`](http://ddili.org/ders/d.en/switch_case.html)

#### 进阶参考

- [详解表达式](https://dlang.org/spec/expression.html)
- [If 表达式规范](https://dlang.org/spec/statement.html#if-statement)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    if (1 == 1)
        writeln("You can trust math in D");

    int c = 5;
    switch(c) {
        case 0: .. case 9:
            writeln(c, " is within 0-9");
            break; // necessary!
        case 10:
            writeln("A Ten!");
            break;
        default: // if nothing else matches
            writeln("Nothing");
            break;
    }
}
```
