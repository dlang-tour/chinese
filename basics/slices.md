# Slices 切片
切片是给定的类型`T`的一组对象`T[]`。切片提供一个`T`类型数组的视图 - 或者直接是一整个数组。 **切片和动态数组是一样的**。

切片由两个成员组成 - 指向起始元素的指针和切片的长度：

    T* ptr;
    size_t length; // 32位程序是32位无符号, 64位是64位无符号

### 通过 new 获得切片

如果创建了一个新的动态数组，则返回这个新分配内存的切片：

    auto arr = new int[5];
    assert(arr.length == 5); // arr.ptr 中引用的内存

在这种情况下，实际分配的内存完全由垃圾收集器 GC 管理。返回的切片充当底层元素的“视图”。

### 获取已存在的内存切片

使用切片运算符可以将切片指向一些已经存在的内存。切片运算符可以应用于另一个切片、静态数组、实现了 `opSlice` 的结构体/类和其他一些实体。

形如 `origin[start .. end]` 的切片操作表示在 `origin` 中获取从 `start` 开始一直到 `end` _之前_ 的所有元素：

    auto newArr = arr[1 .. 4]; // 不包含索引为 4 的元素，即不包含第 5 个元素
    assert(newArr.length == 3);
    newArr[0] = 10; // 修改 newArr[0] 即 arr[1]

切片在现有内存上生成一个新视图。它们*不会创建*一个新的副本。如果不再有切片持有对该内存的引用或者其中*一部分*引用，它将被 GC 释放。

使用切片，可以在只操作一个内存块时（例如 parsers 解析器）编写非常高效的代码，并只对真正需要处理的部分进行切片。通过这种方式，不需要分配新的内存块。

如[上一章](basics/arrays)中，`[$]` 表达式是 `arr.length` 的简写形式。因此 `arr[$]` 是索引到最后一个元素的后面，将触发 `RangeError`（如果没有禁用边界检查）。

### In-depth

- [介绍 D 中的切片](http://dlang.org/d-array-article.html)
- [_D 程序设计_ 切片](http://ddili.org/ders/d.en/slices.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    int[] test = [ 3, 9, 11, 7, 2, 76, 90, 6 ];
    test.writeln;
    writeln("第一个元素： ", test[0]);
    writeln("最后一个元素： ", test[$ - 1]);
    writeln("除了前面两个的其他元素： ",
        test[2 .. $]);

    writeln("切片是一个内存视图：");
    auto test2 = test;
    auto subView = test[3 .. $];
    test[] += 1; // 每一个元素都加 1
    test.writeln;
    test2.writeln;
    subView.writeln;

    // 创建一个空的切片
    assert(test[2 .. 2].length == 0);
}
```
