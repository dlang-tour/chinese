# 导入与模块

即使是一个简单的 HelloWorld 程序，我们也需要用到使用 `import` 语句。
`import` 语句使得给定**模块**中的所有公共方法以及类型可见。

D 叫做 [Phobos](https://dlang.org/phobos/)的标准库中位于 `std` **包** 下，可以使用 `import std.模块名` 的语法来引用其中的模块。

`import` 语句也能够选择性的导入模块内的部分符号：
```d
    import std.stdio : writeln, writefln;
```
选择性导入可以提高代码的可读性，使得符号的来源更加明显，也能够防止来自不同模块的同名符号发生冲突。

`import` 语句不一定要出现在源文件的顶部，它可以在方法内或者是任何作用域内使用。

## {SourceCode}

```d
void main()
{
    import std.stdio;
    //或者 import std.stdio : writeln;
    writeln("Hello World!");
}
```
