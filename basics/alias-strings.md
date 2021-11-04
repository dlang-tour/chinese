# 别名与字符串 Alias & Strings

现在我们知道了数组是什么，已经接触过了`immutable`并对基础类型已经有了一个快速地了解。是时候在一行之内介绍两个新的构造了。

    alias string = immutable(char)[];

术语 `string` 是通过 `alias` 语句定义的 `immutable(char)` 的一个切片。 这意味着，一旦一个`string`被构造，它的内容将不会再被改变了。事实上这是第二个介绍了：欢迎见到UTF-8 `string`!

根据他们的不可变性，`string`可以被不同进程完美共享。因为`string`是一个切片，部分可以在不分配内存的情况下被取出。举例来说, 标准函数[`std.algorithm.splitter`](https://dlang.org/phobos/std_algorithm_iteration.html#.splitter)以新行来分割函数而不需要任何内存分配。

在UTF-8 `string`之下，还有两种更多的类型：

    alias wstring = immutable(wchar)[]; // UTF-16
    alias dstring = immutable(dchar)[]; // UTF-32

<!-- 好吧这个我真的不知道怎么翻译 :P -->
The variants are most easily converted between each other using
the `to` method from `std.conv`:

    dstring myDstring = to!dstring(myString);
    string myString   = to!string(myDstring);

### Unicode字符串 Unicode strings

这意味着单纯的`string`被定义为8位Unicode[编码单元(code units)](http://unicode.org/glossary/#code_unit)的数组。所有数组操作可以被在string中使用，但是他们将会在编码单元级别而不是字符级别工作。在同时，标准库算法将会打断作为一个[编码点(code points)](http://unicode.org/glossary/#code_point)序列的`string`，而且此处也有一个选项来把它们当成[graphemes](http://unicode.org/glossary/#grapheme)序列：通过显式地使用[`std.uni.byGrapheme`](https://dlang.org/library/std/uni/by_grapheme.html)。

这个小例子指出在解析上的不同：

    string s = "\u0041\u0308"; // Ä

    writeln(s.length); // 3

    import std.range : walkLength;
    writeln(s.walkLength); // 2

    import std.uni : byGrapheme;
    writeln(s.byGrapheme.walkLength); // 1

这里`s`实际的数组长度是3，因为它有三个编码单元： `0x41`, `0x03` 和 `0x08`。接下来的2表示单个
Here the actual array length of `s` is 3, because it contains 3 code units:
`0x41`, `0x03` and `0x08`. Those latter two define a single code point
(combining diacritics character) and
[`walkLength`](https://dlang.org/library/std/range/primitives/walk_length.html)
(standard library function to calculate arbitrary range length) counts two code
points total. Finally, `byGrapheme` performs rather expensive calculations
to recognize that these two code points combine into a single displayed
character.

Correct processing of Unicode can be very complicated, but most of the time, D
developers can simply consider `string` variables as magical byte arrays and
rely on standard library algorithms to do the right job.
If by element (code unit) iteration is desired, one can use
[`byCodeUnit`](http://dlang.org/phobos/std_utf.html#.byCodeUnit).

Auto-decoding in D is explained in more details
in the [Unicode gems chapter](gems/unicode).

### Multi-line strings

To create multi-line strings, use the `string str = q{ ... }` syntax.

    string multiline = q{ This
        may be a
        long document
    };

### Raw strings

It is also possible to use raw strings to minimize laborious escaping
of reserved symbols. Raw strings can be declared using either backticks (`` `
... ` ``) or the r(aw)-prefix (`r" ... "`).

    string raw  =  `raw "string"`; // raw "string"
    string raw2 = r"raw "string""; // raw "string"

### In-depth

- [Unicode gem](gems/unicode)
- [Characters in _Programming in D_](http://ddili.org/ders/d.en/characters.html)
- [Strings in _Programming in D_](http://ddili.org/ders/d.en/strings.html)
- [std.utf](http://dlang.org/phobos/std_utf.html) - UTF en-/decoding algorithms
- [std.uni](http://dlang.org/phobos/std_uni.html) - Unicode algorithms

## {SourceCode}

```d
import std.stdio : writeln, writefln;
import std.range : walkLength;
import std.uni : byGrapheme;
import std.string : format;

void main() {
    // format generates a string using a printf
    // like syntax. D allows native UTF string
    // handling!
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

    // Strings are just normal arrays, so any
    // operation that works on arrays works here
    // too!
    import std.array : replace;
    writeln(replace(str, "lö", "lo"));
    import std.algorithm : endsWith;
    writefln("Does %s end with 'rld'? %s",
        str, endsWith(str, "rld"));

    import std.conv : to;
    // Convert to UTF-32
    dstring dstr = to!dstring(str);
    // .. which of course looks the same!
    writeln("My dstring: ", dstr);
}
```
