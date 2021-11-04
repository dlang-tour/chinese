# Documentation 文档

D打算将现代软件工程的重要部分直接继承到语言中。除*合约编程*和*单元测试*，D允许从源代码中本地生成[文档](https://dlang.org/phobos/std_variant.html)。

使用标准格式记录类型和函数，命令`dmd -D`能方便地根据在命令行上传递的源文件生成HTML文档。事实上，整个[Phobos库文档](https://dlang.org/phobos)都是用*DDoc*生成的。

DDoc将以下样式的注释纳入源代码文档中：

* `/// 类型或函数前的三个斜杠`
* `/++ 用了两个+号的多行注释  +/`
* `/** 用了两个*号的多行注释  */`

Have a look at the source code example
to see some standardized documentation
sections.
看看源代码示例以查看一些标准化文档章节。

### In-depth 深入

- [DDoc design](https://dlang.org/spec/ddoc.html)
- [Phobos standard library documentation](https://dlang.org/phobos)

## {SourceCode:incomplete}

```d
/**
  Calculates the square root of a number.

  Here could be a longer paragraph that
  elaborates on the great win for
  society for having a function that is actually
  able to calculate a square root of a given
  number.

  Example:
  -------------------
  double sq = sqrt(4);
  -------------------
  Params:
    number = the number the square root should
             be calculated from.

  License: use freely for any purpose
  Throws: throws nothing.
  Returns: the squrare root of the input.
*/
T sqrt(T)(T number) {
}
```
