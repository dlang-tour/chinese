# Foreach 循环

{{#img-right}}dman-teacher-foreach.jpg{{/img-right}}

D 有一个特性 `foreach` 循环，以获得更少错误、可读性更高的迭代。

### 元素迭代

给定一个 `int[]` 类型的数组 `arr` ，可以使用 `foreach` 循环遍历元素：

    foreach (int e; arr) {
        writeln(e);
    }

`foreach` 循环的第一个字段是每次迭代的变量名。类型是可以自动推断的：

    foreach (e; arr) {
        // typeof(e) 是 int 类型
        writeln(e);
    }

第二个字段必须是一个数组 - 或者一个称为 *range 范围* 的特殊可迭代对象，[下节](basics/ranges)将会介绍。

### 通过引用访问

每个元素在数组迭代时会被拷贝。通常对于基础类型是可以接受的，但对于大的类型可能会有问题。
为了防止复制或者需要 `直接` 修改，可以使用 `ref`：

    foreach (ref e; arr) {
        e = 10; // overwrite value
    }

### 迭代 `n` 次

D 允许我们编写执行 `n` 次的迭代器，使用更简洁的 `..` 语法：

    foreach (i; 0 .. 3) {
        writeln(i);
    }
    // 0 1 2

`a .. b` 的最后一个数字不会包括的循环范围内，在这里就是执行了循环 `3` 次。

### 用索引计数器迭代

对于数组，还可以访问单独的索引变量。

    foreach (i, e; [4, 5, 6]) {
        writeln(i, ":", e);
    }
    // 0:4 1:5 2:6

### 反向迭代器 `foreach_reverse`

集合可以使用 `foreach_reverse` 来反向迭代：

    foreach_reverse (e; [1, 2, 3]) {
        writeln(e);
    }
    // 3 2 1

### 深入了解

- [_D 程序设计_ `foreach`](http://ddili.org/ders/d.en/foreach.html)
- [_D 程序设计_ `foreach` with Structs and Classes ](http://ddili.org/ders/d.en/foreach_opapply.html)
- [`foreach` specification](https://dlang.org/spec/statement.html#ForeachStatement)

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
