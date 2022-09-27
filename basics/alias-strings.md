# Alias & Strings 别名 & 字符串

现在我们已经知道了什么是数组，什么是 `immutable` 不可变，并且已经快速浏览的基础类型，现在在一行内介绍两个新的概念：

    alias string = immutable(char)[];

首先 `alias` 别名定义了 `string` ，是一个 `immutable(char)[]` 不可变的 char 数组。这意味着，一旦 `string` 被构造里面的数据将不会被改变。实际上这就是第二个概念 UTF-8 字符串 `string`！

由于 `string` 的不可变性，所以它可以完美的在不同的线程共享。并且 `string` 是切片，所以它可以在不重新分配内存的情况下获取部分内容。例如，标准库函数 [`std.algorithm.splitter`](https://dlang.org/phobos/std_algorithm_iteration.html#.splitter) 可以在不分配新内存下拆分字符串。

除了 UTF-8 字符串 `string` 外还有两个类型：

    alias wstring = immutable(wchar)[]; // UTF-16 字符串
    alias dstring = immutable(dchar)[]; // UTF-32 字符串

使用 `std.conv` 中的 `to` 方法可以很方便在变量之间转换：

    dstring myDstring = to!dstring(myString);
    string myString   = to!string(myDstring);

### Unicode 字符串

普通的 `string` 被定义为 8 位 [Unicode 编码](http://unicode.org/glossary/#code_unit)。 所有的数组操作都可以用在字符串上，但是他们在 code unit 编码单元上操作，而不是字符级别。于此同时，标准库算法将 `string` 解释为 [code points 编码点](http://unicode.org/glossary/#code_point) 序列，并且可以通过显式调用 [`std.uni.byGrapheme`](https://dlang.org/library/std/uni/by_grapheme.html) 转为成 [graphemes 字符](http://unicode.org/glossary/#grapheme) 序列。 

这个小例子说明了解释编码上的差异：

    string s = "\u0041\u0308"; // Ä

    writeln(s.length); // 3

    import std.range : walkLength;
    writeln(s.walkLength); // 2

    import std.uni : byGrapheme;
    writeln(s.byGrapheme.walkLength); // 1

这里 `s` 的实际数组长度是3，因为它包含三个编码单元：`0x41`, `0x03` 和 `0x08`。后面两个定义了一个编码点（读音符号补充），而[`walkLength`](https://dlang.org/library/std/range/primitives/walk_length.html)（标准库中计算任意范围的长度的函数）共计算了两个编码点。
最后，`byGrapheme` 执行相当昂贵的计算，从而认识到这两个编码点会合并显示成一个字符。

正确处理 Unicode 可能非常复杂，但是在大多数情况下 D 开发者可以将 `string` 视为一个神奇的字节数组，然后依赖标准库算法来完成正确的工作。如果需要按照编码单元来迭代可以使用
[`byCodeUnit`](http://dlang.org/phobos/std_utf.html#.byCodeUnit)。

[Unicode gems](gems/unicode) 章节中的自动解码有更详细的解释。

### 多行字符串

在 D 中字符串总是可以跨行的：

    string multiline = "
    This
    may be a
    long document
    ";

当文档中出现引号时，可以使用所见即所得字符串（见下文或[文档](http://dlang.org/spec/lex.html#delimited_strings)）。

### Wysiwyg strings 所见即所得字符串

可以使用原始字符串来降低费力的转义操作。原始字符串可以使用反引号（`` `
... ` ``）或者 r 前缀 （`r" ... "`）。

    string raw  =  `raw "string"`; // raw "string"
    string raw2 = r"raw `string`"; // raw `string`

D 提供了更多表示字符串的方法 - 不要犹豫，去探索[它们](https://dlang.org/spec/lex.html#string_literals)吧。

### 深入了解

- [Unicode gem](gems/unicode)
- [_D 程序设计_ 字符](http://ddili.org/ders/d.en/characters.html)
- [_D 程序设计_ 字符串](http://ddili.org/ders/d.en/strings.html)
- [std.utf](http://dlang.org/phobos/std_utf.html) - UTF 编码/解码算法
- [std.uni](http://dlang.org/phobos/std_uni.html) - Unicode 算法
- [String Literals in the D spec](http://dlang.org/spec/lex.html#string_literals)

## {SourceCode}

```d
import std.stdio : writeln, writefln;
import std.range : walkLength;
import std.uni : byGrapheme;
import std.string : format;

void main() {
    // 格式化字符串使用像 printf 一样的语法，
    // D 原生支持处理 UTF 字符串！
    string str = format("%s %s", "Hellö",
        "Wörld");
    writeln("My string: ", str);
    writeln("Array length (code unit count)"
        ~ " of string: ", str.length);
    writeln("Range length (code point count)"
        ~ " of string: ", str.walkLength);
    writeln("Character length (grapheme count)"
        ~ " of string: ",
        str.byGrapheme.walkLength);

    // 字符串只是普通的数组，
    // 所以任何数组操作都可以使用！
    import std.array : replace;
    writeln(replace(str, "lö", "lo"));
    import std.algorithm : endsWith;
    writefln("Does %s end with 'rld'? %s",
        str, endsWith(str, "rld"));

    import std.conv : to;
    // 转换成 UTF-32
    dstring dstr = to!dstring(str);
    // .. 当然打印出来是一样的！
    writeln("My dstring: ", dstr);
}
```
