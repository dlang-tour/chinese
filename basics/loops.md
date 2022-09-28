# Loops 循环

D 提供四种循环结构。

### 1) `while`

`while` 循环在满足特定条件时执行代码块：

    while (condition)
    {
        foo();
    }

### 2) `do ... while`

`do .. while` 循环在满足特定条件时执行代码块, 但是在评估 `while` 条件前 __语句块__ 会被先执行：

    do
    {
        foo();
    } while (condition);

### 3) 经典的 `for` 循环
C/C++/Java 经典的 `for` 循环，包含 __初始化__、 __循环条件__ 和 __循环语句__：

    for (int i = 0; i < arr.length; i++)
    {
        ...

译者注：如果没有接触过 C/C++/Java，`for` 循环可以当成如下等价 `while` 代码

    int i = 0;
    while (i < arr.length)
    {
        ...
        i++;
    }


### 4) `foreach`

[`foreach`](basics/foreach) 循环将在下一张详细介绍：

    foreach (el; arr)
    {
        ...
    }

#### 特殊的关键字和标签

特殊的关键字 `break` 将立即终止当前循环。
在内嵌的循环中 _标签_ 可以终止任意外部循环：

    outer: for (int i = 0; i < 10; ++i)
    {
        for (int j = 0; j < 5; ++j)
        {
            ...
            break outer;

关键字 `continue` 不再执行接下来的语句，直接开始执行下一个循环迭代。

### In-depth

- [_D 程序设计_ `for`](http://ddili.org/ders/d.en/for.html)
- [`for` specification](https://dlang.org/spec/statement.html#ForStatement)
- [_D 程序设计_ `while`](http://ddili.org/ders/d.en/while.html)
- [`while` specification](https://dlang.org/spec/statement.html#WhileStatement)
- [_D 程序设计_ `do-while`](http://ddili.org/ders/d.en/do_while.html)
- [`do-while` specification](https://dlang.org/spec/statement.html#do-statement)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Computes the average of
the elements of an array.
*/
double average(int[] array)
{
    immutable initialLength = array.length;
    double accumulator = 0.0;
    while (array.length)
    {
        // this could be also done with .front
        // with import std.array : front;
        accumulator += array[0];
        array = array[1 .. $];
    }

    return accumulator / initialLength;
}

void main()
{
    auto testers = [ [5, 15], // 20
          [2, 3, 2, 3], // 10
          [3, 6, 2, 9] ]; // 20

    for (auto i = 0; i < testers.length; ++i)
    {
      writeln("The average of ", testers[i],
        " = ", average(testers[i]));
    }
}
```
