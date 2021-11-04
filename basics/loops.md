# 循环

D 提供四种循环结构.

### 1) `while`

当满足特定的条件时，
`while` 循环执行给定的代码块:

    while (condition)
    {
        foo();
    }

### 2) `do ... while`

当满足特定的条件时，`do .. while` 循环也会执行给定的代码块, 但是与 `while` 相反的是，在循环条件被第一次求值之前， _循环体_ 就将会被执行了。0

    do
    {
        foo();
    } while (condition);

### 3) 经典 `for` 循环


经典的for循环在C/C++ 或 Java 中被人们所知，包括 _初始化语句_, _循环条件_ 和 _循环表达式_:

    for (int i = 0; i < arr.length; i++)
    {
        ...

### 4) `foreach`

[`foreach` 循环](basics/foreach) 将会在下一个章节中进行详细的介绍：

    foreach (el; arr)
    {
        ...
    }

#### 特殊的关键词和标签

特殊关键词`break`将会立即打断当前循环。在一个嵌套循环中，一个 _标签_ 可以用于跳出到任意外部循环:

    outer: for (int i = 0; i < 10; ++i)
    {
        for (int j = 0; j < 5; ++j)
        {
            ...
            break outer;

关键词`continue` 会开启下一个循环迭代。

### 深入

- `for` 循环 在 [_D程序设计_](http://ddili.org/ders/d.en/for.html), [规范](https://dlang.org/spec/statement.html#ForStatement)
- `while` 循环 在 [_D程序设计_](http://ddili.org/ders/d.en/while.html), [规范](https://dlang.org/spec/statement.html#WhileStatement)
- `do-while` 循环 在 [_D程序设计_](http://ddili.org/ders/d.en/do_while.html), [规范](https://dlang.org/spec/statement.html#do-statement)

## {SourceCode}

```d
import std.stdio : writeln;

/*
计算一个数组中元素的平均值
*/
double average(int[] array)
{
    // 数组的.empty属性不是D中原生的
    // 但已经可以通过
    // 从std.array中引入这个函数来使用
    import std.array : empty, front;

    double accumulator = 0.0;
    auto length = array.length;
    while (!array.empty)
    {
        // 对于.front属性也是这样
        // 通过 import std.array : front;
        accumulator += array[0];
        array = array[1 .. $];
    }

    return accumulator / length;
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
