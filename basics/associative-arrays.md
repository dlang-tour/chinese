# Associative Arrays 关联数组

D 语言具有内置的*关联数组*，也被称为哈希映射（HashMap）。一个键类型为 `string` 、值类型为 `int` 的关联数组声明如下：

    int[string] arr;

值可以通过对应的键访问，因此赋值语句可以这样写：

    arr["key1"] = 10;

要测试键是否在关联数组中，可以使用 `in` 表达式：

    if ("key1" in arr)
        writeln("Yes");

如果可以找到值，那么 `in` 表达式返回一个指向该值的指针，否则返回一个 `null` 指针。 因此可以方便地将检查和写入结合起来：

    if (auto val = "key1" in arr)
        *val = 20;

访问一个不存在的键会产生一个 `RangeError` ，它会立即中止应用程序。 对于具有默认值且安全的访问方式，可以使用 `get(key, defaultValue)` 。

关联数组具有类似数组的 `.length` 属性，并提供一个 `.remove(val)` 成员函数以通过其键来删除条目。作为练习，读者可以探索特殊的 `.byKey` 和 `.byValue` 。

### 深入了解

- [_D程序设计_ 关联数组](http://ddili.org/ders/d.en/aa.html)
- [Associative arrays specification](https://dlang.org/spec/hash-map.html)
- [std.array.byPair](http://dlang.org/phobos/std_array.html#.byPair)

## {SourceCode}

```d
import std.array : assocArray;
import std.algorithm.iteration: each, group,
    splitter, sum;
import std.string: toLower;
import std.stdio : writefln, writeln;

void main()
{
    string text = "Rock D with D";

    // 一次性迭代所有的单词并统计出现次数
    int[string] words;
    text.toLower()
        .splitter(" ")
        .each!(w => words[w]++);

    foreach (key, value; words)
        writefln("key: %s, value: %d",
                       key, value);

    // `.keys` 和 `.values` 返回数组
    writeln("Words: ", words.keys);

    // `.byKey`, `.byValue` and `.byKeyValue`
    // 返回惰性求值的可迭代范围
    writeln("# Words: ", words.byValue.sum);

    // 新的关联数组可以通过向 `assocArray`
    // 传递键值对元组生成
    auto array = ['a', 'a', 'a', 'b', 'b',
                  'c', 'd', 'e', 'e'];

    // `.group` 群组连续相等的元素到
    // 一个存有元素和其数量的元组中
    auto keyValue = array.group;
    writeln("Key/Value range: ", keyValue);
    writeln("Associative array: ",
             keyValue.assocArray);
}
```
