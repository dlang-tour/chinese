# 关联数组

D 语言具有内置的*关联数组*，也被称为哈希映射（HashMap）。一个键类型为`string`、值类型为`int`的关联数组声明如下：

    int[string] arr;

该值可以通过对应的键访问，因此赋值语句可以这样写：

    arr["key1"] = 10;

要测试键是否在关联数组中，可以使用 `in` 表达式：

    if ("key1" in arr)
        writeln("Yes");

如果可以找到值，那么 `in` 表达式返回一个指向该值的指针，否则返回一个 `null` 指针。 因此可以方便地将检查和写入结合起来：

    if (auto val = "key1" in arr)
        *val = 20;

访问一个不存在的键会产生一个“RangeError”，它会立即中止应用程序。 对于具有默认值且安全的访问方式，可以使用`get(key, defaultValue)`。

关联数组具有类似数组的 `.length` 属性，并提供一个 `.remove(val)` 成员函数以通过其键来删除条目。 作为练习，读者可以探索特殊的`.byKey`和`.byValue`。

### 深入了解

- [D语言中的关联数组](http://ddili.org/ders/d.en/aa.html)
- [关联数组规范](https://dlang.org/spec/hash-map.html)
- [元组迭代](http://dlang.org/phobos/std_array.html#.byPair)

## {源代码}

```d
import std.stdio : writeln;

/**
  将给定的文本拆分为单词并返回一个关联数组，
  并将该数组中的单词映射到对应出现的次数。

Params:
    text = 待拆分的文本
*/
int[string] wordCount(string text)
{
    // 函数 splitter 将输入的语句拆分为一个数据范围
    import std.algorithm.iteration : splitter;
    import std.string : toLower;

    // 按词索引并返回对应单词出现的次数
    // 到类型为int[string]的关联数组中；
    foreach(word; splitter(text.toLower(), " "))
    {
        // 如果找到单词就增加对应的出现次数
        // 整数的默认值是0.
        words[word]++;
    }

    return words;
}

void main()
{
    string text = "D is a lot of fun";

    auto wc = wordCount(text);
    writeln("Word counts: ", wc);

    // 可以使用的迭代方式:
    // byKey, byValue, byKeyValue
    foreach (word; wc.byValue)
        writeln(word);
}
```
