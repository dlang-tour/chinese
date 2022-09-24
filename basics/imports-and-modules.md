# Imports and modules 导入与模块

{{#img-right}}turtle.svg{{/img-right}}

D的核心设计决策之一是一致性，避免语言中的极端情况。
这叫做[海龟下面都是海龟](https://en.wikipedia.org/wiki/Turtles_all_the_way_down)（译者注：表一个循环，世界被海龟拖着，表明这个海龟被一个更大的海龟拖着，无限循环）。
关于这种一致性的一个很好的例子是导入 `import`。

## 导入

即使是一个简单的 HelloWorld 程序，我们也需要用到使用 `import` 语句。
`import` 语句使得给定**module 模块**中的所有公共方法以及类型可见。

### 海龟下面都是海龟

`import` 语句 __不一定__ 要出现在源文件的顶部，它可以在方法内或者是任何作用域内使用。
这个概念会出现在后面的很多章节中，D 不会对您强加任意的限制。

### 选择性导入

D 叫做 [Phobos](https://dlang.org/phobos/) 的标准库中位于 `std` **package 包** 下，
可以使用 `import std.模块名` 的语法来引用其中的模块 。

`import` 语句也能够选择性的导入模块内的部分符号:

    import std.stdio : writeln, writefln;

选择性导入可以提高代码的可读性，使得符号的来源更加明显，也能够防止来自不同模块的同名符号发生冲突。

### 导入与目录和文件匹配

与其他系统相比，D 的模块系统完全基于文件。
例如，`my.cat` 始终指向文件夹 `my/` 中的文件 `cat.d`，
文件夹 `my` 需要在当前工作目录中或从 `-I` 中明确指定的目录导入。
有时，为了将大模块简化分成多个较小的文件，而不是一个 `cat.d` ，也可以使用文件夹 `cat/` 。
D 编译器会尝试加载 `my/cat/package.d` 而不是 `my/cat.d`。

通常约定(但不是硬性规则)，`package.d` 文件会公开导入同一文件夹中的所有其他模块。

## {SourceCode}

```d
void main()
{
    import std.stdio;
    //或者 import std.stdio : writeln;
    writeln("Hello World!");
}
```
