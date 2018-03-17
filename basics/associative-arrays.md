# 关联数组 Associative Arrays

D 有内置的 *关联数组associative arrays* 也就是所知的哈希字典。
一个关联数组的键类型为`string`，值类型为`int`，关联数组声明如下:

    int[string] arr;

这个值可以被通过它的键进行存取，因此（它的键）为集合:

    arr["key1"] = 10;

为了测试是否一个键在一个关联数组中，可以使用`in`表达式：

    if ("key1" in arr)
        writeln("Yes");

如果（键）在（关联数组中）可以被找到，`in`表达式会返回指向值的指针，否则返回一个`null`指针。因此存在性检查和写入可以被方便地结合：

    if (auto val = "key1" in arr)
        *val = 20;

取用一个不存在的键会产生一个`RangeError`那会立即终止应用程序。为了安全地存取默认值，可以使用`get(key, defaultValue)`。

关联数组就像数组一样有`.length`属性，并且提供一个`.remove(val)`成员来通过它们的键移除表项。这里留下一个练习给读者来浏览特殊的`.byKey`和`.byValue`范围(Range)。

### In-depth

- [_D程序设计_ 中的关联数组](http://ddili.org/ders/d.en/aa.html)
- [关联数组规范](https://dlang.org/spec/hash-map.html)
- [std.array.byPair](http://dlang.org/phobos/std_array.html#.byPair)

## {SourceCode}

```d
import std.stdio : writeln;

/**
分割给定的文字为单词并返回一个映射单词到各自单词数量的关联数组.

参数:
    text = 将被分割的文字
*/
int[string] wordCount(string text)
{
    // 这个函数分割者惰性地将输入
    // 分割为一个范围
    import std.algorithm.iteration : splitter;
    import std.string : toLower;

    // 按词索引并返回计数
    int[string] words;

    foreach(word; splitter(text.toLower(), " "))
    {
        // 如果找到了单词
        // 就增加它的计数
        // 整型默认为0
        words[word]++;
    }

    return words;
}

void main()
{
    string text = "D is a lot of fun";

    auto wc = wordCount(text);
    writeln("Word counts: ", wc);

    // 可能的迭代器:
    // byKey, byValue, byKeyValue
    foreach (word; wc.byValue)
        writeln(word);
}
```
