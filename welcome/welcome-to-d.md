# 欢迎

欢迎来到与 *D 程序设计语言* 的互动之旅。

{{#dmanmobile}}

本教程将带你走近这个 __强大__ ，富有 __表现力__ 的，能够编译为 __高效__ 的 __本地代码__ 的程序设计语言。

{{/dmanmobile}}

### What is D?

D 语言是现代编译器的集大成者，它还拥有着下面这些特性：

{{#dmandesktop}}

- _高层次_的语言结构提供了强大的建模能力
- _高性能_，编译型语言
- 静态类型
- 提供系统 API 以及对于硬件的直接接口
- 快速编译
- 内存安全的子集（Safe D）
- _可维护的_，_易于理解的_ 代码
- 平缓的学习曲线（类似 C，Java 等的语法）
- 兼容 C 的二进制接口
- 有限制的兼容 C++ 的二进制接口
- 多范式（命令式，结构化，面向对象，通用，纯函数式，甚至包括汇编语言）
- 内置的错误检测支持（契约，单元测试）

... 还有更多 [特性](http://dlang.org/overview.html).

{{/dmandesktop}}

### 关于本教程

本教程的每一部分都附带了一个源代码实例，可以通过修改这些例子来体验 D 语言的特性。单击 _run_ 按钮（或者使用 `Ctrl-enter` 快捷键）来编译并运行它。

你可以通过点击下方的链接"`<` 上一节" 和 "下一节 `>`"， 或按左右方向键来浏览这个互动之旅，也可以通过点击页面上方的目录直接进入某个章节。

### 协作

本教程是 [开源](https://github.com/dlang-tour) 的
我们欢迎通过 _pull requests_ 的方式来完善本教程。

## {SourceCode}

```d
import std.stdio;
import std.algorithm;
import std.range;

void main()
{
    // 让我们开始吧！
    writeln("Hello World!");
    
    // 一个给有经验的程序员的例子：
    // 给定三个数组，在不额外请求内存的情况下，
    // 对里面所有的数进行原地排序。
    int[] arr1 = [4, 9, 7];
    int[] arr2 = [5, 2, 1, 10];
    int[] arr3 = [6, 8, 3];
    sort(chain(arr1, arr2, arr3));
    writefln("%s\n%s\n%s\n", arr1, arr2, arr3);
    // 想要进一步了解本例，请参见
    // “Gems” 章的 “Range algorithms” 小节
}
```
