# Range algorithms

标准模块[std.range](http://dlang.org/phobos/std_range.html)
和 [std.algorithm](http://dlang.org/phobos/std_algorithm.html)
提供了可以以可阅读的方式表达复杂操作的多个伟大的函数 - 基于将*range* 作为构建块。

这些算法的伟大之处在于，只需定义自己的range，你就可以直接从标准库中的已有内容中获益。

### std.algorithm

`filter` - 接受一个lambda作为模板参数，产生一个新的过滤元素的range:

    filter!"a > 20"(range);
    filter!(a => a > 20)(range);

`map` -使用 定义为模板参数的谓词 产生一个新的range:

    [1, 2, 3].map!(x => to!string(x));

`each` - Poor man's `foreach` as a range crunching
function:

    [1, 2, 3].each!(a => writeln(a));

### std.range
`take` - 限制到 *N* 个元素:

    theBigBigRange.take(10);

`zip` - 在两个range之间平行迭代，每次迭代返回每个range的数组：

    assert(zip([1,2], ["hello","world"]).front
      == tuple(1, "hello"));

`generate` - 接收一个函数，并返回一个 每次循环都调用它产生的 range，例如:

    alias RandomRange = generate!(x => uniform(1, 1000));

`cycle` - 返回永远重复给定range的range：

    auto c = cycle([1]);
    // range永远不会为空!
    assert(!c.empty);

### 这份文档等着你来读!


### 深入

- [_D程序设计_ 中的 range](http://ddili.org/ders/d.en/ranges.html)
- [_D程序设计_ 中的 更多 Range](http://ddili.org/ders/d.en/ranges_more.html)

## {SourceCode}

```d
// 来吧, 拿上这个武器!
import std.algorithm : canFind, map,
  filter, sort, uniq, joiner, chunkBy, splitter;
import std.array : array, empty;
import std.range : zip;
import std.stdio : writeln;
import std.string : format;

void main()
{
    string text = q{This tour will give you an
overview of this powerful and expressive systems
programming language which compiles directly
to efficient, *native* machine code.};

    // 分割参数
    alias pred = c => canFind(" ,.\n", c);
    // 作为一个不需要分配内存
    // 而只是惰性工作的好算法
    auto words = text.splitter!pred
      .filter!(a => !a.empty);

    auto wordCharCounts = words
      .map!"a.count";

    // 以不错的方式，从最少字符的开头，输出每个单词的字符数！
    zip(wordCharCounts, words)
      // 转化为数组以排序
      .array()
      .sort()
      // 我们不需要复制一份，对吧？
      .uniq()
      // put all in one row that have the
      // same char count. chunkBy helps
      // us here by generating ranges
      // of range that are chunked by the length
      .chunkBy!(a => a[0])
      // 这些元素将在一行之内被连接起来。
      .map!(chunk => format("%d -> %s",
          chunk[0],
          // 只要单词
          chunk[1]
            .map!(a => a[1])
            .joiner(", ")))
      // joiner joins, but lazily!
      // and now the lines with the line
      // feed
      .joiner("\n")
      // 到stdout的管道
      .writeln();
}
```
