# 统一函数调用语法 Uniform Function Call Syntax (UFCS)

**UFCS** 是D的一个关键特性，通过良定的封装来实现代码的可重用性和可扩展性。

UFCS 允许任何对自由函数`fun(a)`的调用都可以写成成员函数调用`a.fun()`。

如果编译器看到`a.fun()`，并且该类型没有名为`fun()`的成员函数，它将尝试查找第一个参数与`a`匹配的全局函数。

当使用复杂的链式函数调用时，这个特性特别有用。不用写
    foo(bar(a))

现在可以写成

    a.bar().foo()

此外，在D中，不必给没参数的函数加括号，这意味着 _任意_ 函数都可以像property一样使用：

    import std.uni : toLower;
    "D rocks".toLower; // "d rocks"

UFCS在处理*range*时尤其重要，它可以将几种算法放在一起执行复杂的操作，使代码清晰易管理。

    import std.algorithm : group;
    import std.range : chain, retro, front, retro;
    [1, 2].chain([3, 4]).retro; // 4, 3, 2, 1
    [1, 1, 2, 2, 2].group.dropOne.front; // tuple(2, 3u)

### 深入

- [_D 程序设计_ 中的UFCS](http://ddili.org/ders/d.en/ufcs.html)
- [_统一函数调用语法_](http://www.drdobbs.com/cpp/uniform-function-call-syntax/232700394) by Walter Bright
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode}

```d
import std.stdio : writefln, writeln;
import std.algorithm.iteration : filter;
import std.range : iota;

void main()
{
    "Hello, %s".writefln("World");

    10.iota // 返回0到9的数字
      // 偶数的过滤器(filter)
      .filter!(a => a % 2 == 0)
      .writeln(); // 把它们写到标准输出流stdout

    // 传统方式
    writeln(filter!(a => a % 2 == 0)
    			   (iota(10)));
}
```
