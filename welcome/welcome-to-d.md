# welcome

欢迎来到与 *D 程序设计设计语言* 的互动之旅.

{{#dmanmobile}}

本教程将带你走近这个 __强大__ 富有 __表现力__ 的，能够直接编译到 __高效__ 的 __本地代码__ 的程序设计语言。

{{/dmanmobile}}

### 什么是 D?

D 是现代编译器的集大成者，并且它拥有着下列独特的特性:

{{#dmandesktop}}

- _high level_ constructs for great modeling power
- _高性能的_ _编译型_ 语言
- 静态类型
- 系统 API 以及硬件的直接接口
- 很短的编译时间
- 内存安全的子集 (SafeD)
- _可维护的_，_易于理解的_ 代码
- 平缓的学习曲线 (类 C 语法，和 Java 等语言相似)
- 兼容 C 程序的二进制接口
- 对兼容 C++ 程序的二进制接口的有限支持
- 多范式 (命令式，结构化，面向对象，通用化，纯函数式，and even assembly)
- 内置的错误检测能力 (契约，单元测试)

... 还有更多功能 [features](http://dlang.org/overview.html).

{{/dmandesktop}}

### 关于本教程

每一部分都附带了一个源代码示例，可以通过修改例子来实验 D 的语言特性。
单击 _run_ 按钮 (或者使用 `Ctrl-enter` 快捷键) 来编译运行它。

### 协作

本教程是[开源的](https://github.com/dlang-tour)
我们欢迎您通过提交 pull requests 来完善本教程，使它变得更好。

## {SourceCode}

```d
import std.stdio;
import std.algorithm;
import std.range;

void main()
{
    // 让我们开始吧!
    writeln("Hello World!");

    // 对于有经验的程序的例子:
    // 在不进行动态分配内存的情况下，
    // 创建了三个数组，并对它们进行了排序
    int[] arr1 = [4，9，7];
    int[] arr2 = [5，2，1，10];
    int[] arr3 = [6，8，3];
    sort(chain(arr1，arr2，arr3));
    writefln("%s\n%s\n%s\n"，arr1，arr2，arr3);
    // To learn more about this example，see the
    // "Range algorithms" page under "Gems"
}
```
