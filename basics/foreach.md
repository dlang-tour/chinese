# Foreach循环

{{#img-right}}dman-teacher-foreach.jpg{{/img-right}}

D实现了一个更不易出错和有更好的可读性迭代的“foreach”循环

### 元素迭代

给定一个类型为`int []`的数组`arr`，可以使用`foreach`循环遍历元素：

    foreach (int e; arr) {
        writeln(e);
    }

`foreach` 定义中的第一个字段是循环迭代中使用的变量。 它的类型是自动推导的：

    foreach (e; arr) {
        // typeof(e) is int
        writeln(e);
    }

第二个字段必须是一个数组 - 或者一个特殊的可迭代对象，将在[下一节](basics/ranges)中介绍，称为**Range**

### 通过引用存取

元素将在迭代过程中从数组或Range中复制。
这对基本类型是可以接受的，但对于大的类型
会是一个问题。 为了防止复制或允许 *就地(in-place)*
更改，可以使用`ref`：

    foreach (ref e; arr) {
        e = 10; // 覆写原值
    }

### 迭代 `n` 次

D允许编写应该执行`n`次的迭代，用`..`语法更简洁：

    foreach (i; 0 .. 3) {
        writeln(i);
    }
    // 0 1 2

`a .. b`中的最后一个数字不在范围内，
因此循环体执行了3次。

### 使用索引计数器（index counter）迭代

对于数组, 也可以存取一个分割的索引变量。

    foreach (i, e; [4, 5, 6]) {
        writeln(i, ":", e);
    }
    // 0:4 1:5 2:6

### 使用 `foreach_reverse` 进行反向迭代

一个集合可以使用`foreach_reverse`以相反的顺序进行迭代:

    foreach_reverse (e; [1, 2, 3]) {
        writeln(e);
    }
    // 3 2 1

### 深入

- [_D程序设计_ 中的 `foreach`](http://ddili.org/ders/d.en/foreach.html)
- [_D程序设计_ 中的 对结构体和类的 `foreach`](http://ddili.org/ders/d.en/foreach_opapply.html)
- [`foreach` 规范](https://dlang.org/spec/statement.html#ForeachStatement)

## {SourceCode}

```d
import std.stdio : writefln;

void main() {
    auto arr = [
        [5, 15],      // 20
        [2, 3, 2, 3], // 10
        [3, 6, 2, 9], // 20
    ];

    foreach (i, row; arr)
    {
        double total = 0.0;
        foreach (e; row)
            total += e;

        auto avg = total / row.length;
        writefln("AVG [row=%d]: %.2f", i, avg);
    }
}
```
