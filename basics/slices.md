# Slices

给定类型了 `T`，切片是类型为 `T[]` 的对象。
切片提供了指向数组子集或者整个数组的方法.
**切片和动态数组是一样的.**

一个切片由指向开始节点的指针和切片长度两部分组成:

    T* ptr;
    size_t length; // 32位系统上长度为32,64位系统上长度为64

### 从新分配的数组里获得切片

如果创建了一个动态数组,则返回这个新分配内存块的切片

    auto arr = new int[5];
    assert(arr.length == 5); // memory referenced in arr.ptr

在这种情况下实际分配的内存完全由垃圾回收器管理. 返回的切片作为底层元素的“视图”。

### 从已存在的内存块中获取分片

使用切片操作符也可以指向已经存在的内存块.  切片操作符可以应用于另一个切片,数组,实现了`opSlice`的结构/类和其他几个实体.

在表达式`origin[start .. end]`的示例中,切片运算符用于从`起始`到`结束`前的元素获取`origin`元素的切片：

    auto newArr = arr[1 .. 4]; // 索引4不包括在切片中
    assert(newArr.length == 3);
    newArr[0] = 10; // 修改newArr[0]

这样切片在现有的内存块上生成新的视图时 *不会* 创建新的副本. 
如果没有切片保留对该内存的引用 它将被垃圾收集器释放.

使用切片，可以为仅在一个内存块上操作的东西（例如解析器）编写非常高效的代码,并且只切割他们真正需要处理的部分,这样就不需要分配新的内存了.

像上一节[previous section](basics/arrays)说的,  `[$]` 表达式是`arr.length`的缩写形式.
因此 `arr[$]` 将索引至切片结束 ,并会生成`RangeError`（如果bounds-checking未被禁用).

### 更深入的参考

- [Introduction to Slices in D](http://dlang.org/d-array-article.html)
- [Slices in _Programming in D_](http://ddili.org/ders/d.en/slices.html)

## {代码示例}

```d
import std.stdio : writefln;

/**
  使用分片,递归计算出所有值的最小值.对于每个递归
  调用一个子切片,因此我们没有创建新的拷贝,不会有新的内存分配.
*/
int minimum(int[] slice)
{
    assert(slice.length > 0);
    if (slice.length == 1)
        return slice[0];
    auto otherMin = minimum(slice[1 .. $]);
    return slice[0] < otherMin ?
        slice[0] : otherMin;
}

void main()
{
    int[] test = [ 3, 9, 11, 7, 2, 76, 90, 6 ];
    auto min = minimum(test);
    writefln("The minimum of %s is %d",
        test, min);
    assert(min == 2);
}
```
