# Basic types 基本类型

D 提供了很多基本类型，基本类型的大小**与平台无关**。唯一的例外是 `real` 类型表示平台提供的最高精度的浮点数类型。无论是使用 32 位的系统还是 64 位的系统编译，整数类型的大小总是不变的。

| 类型                           | 大小
|-------------------------------|------------
|`bool`                         | 8 位
|`byte`, `ubyte`, `char`        | 8 位
|`short`, `ushort`, `wchar`     | 16 位
|`int`, `uint`, `dchar`         | 32 位
|`long`, `ulong`                | 64 位

#### 浮点类型:

| 类型     | 大小
|---------|--------------------------------------------------
|`float`  | 32 位
|`double` | 64 位
|`real`   | >= 64 位 (一般为 64 位, 但是在 Intel x86 32-bit 平台上时为 80 位)

前缀 `u` 代表 *无符号* 类型. `char` 类型会转换为
UTF-8 码元, `wchar` 类型用于 UTF-16 字符串，而 `dchar` 用于 UTF-32 字符串.

不同类型的变量之间只有在不会丢失精度时才会发生隐式转换。但是编译器允许浮点类型之间发生隐式转换（例如从 `double` 到 `float` 的隐式转换）。

可以使用 `cast(类型)变量` 表达式来强制转换变量的类型。强制转换需要小心使用，因为它可能会打破类型系统。

可以使用特殊关键字 `auto` 来创建一个变量，变量的类型会被推断为右侧的初始化表达式的类型。例如，`auto myVar = 7`中，`myVar` 的类型会被推断为 `int`。请注意，使用 `auto` 声明变量显式写出变量类型进行声明是一样的，变量的类型在编译时被确定的，而不能够被更改。

### Type properties 类型属性

所有的数据类型都具有表示该类型初始值的 `.init` 属性，所有整数类型的 `.init` 属性的值都为 `0`，而浮点数为 `nan` (*not a number 非数字*).

整数以及浮点数类型具有表示该类型最大值的 `.max` 属性。
整数类型也具有表示该类型最小值的 `.min` 属性，而浮点数类型都具有被定义为该类型非0的最小可表示的常规值的 `.min_normal` 属性。

浮点数类型还具有属性 `.nan` （NaN 值），`.infinity`
（无穷大值），`.dig`（小数精度位数），`.mant_dig`
(尾数的位数) 以及其他属性。

每个类型也都具有一个用来表示类型名称的字符串属性 `.stringof`。

### D 中的索引

在 D 中，索引的类型通常为别名类型 `size_t`，因为这个类型足够大，可以用来表示所有可寻址内存的偏移量 - 在 32 位架构中它为 `uint`，在 64 为架构中它为 `ulong`。

### Assert 断言

`assert` 是在调试模式下验证条件，并在失败时抛出 `AssertionError` 错误终止程序的表达式。
`assert(0)` 用来标记不可达的代码。

### 深入

#### 基本参考

- [赋值](http://ddili.org/ders/d.en/assignment.html)
- [变量](http://ddili.org/ders/d.en/variables.html)
- [运算](http://ddili.org/ders/d.en/arithmetic.html)
- [浮点数](http://ddili.org/ders/d.en/floating_point.html)
- [_D编程中的_ 基本类型](http://ddili.org/ders/d.en/types.html)

#### Advanced references

- [D 中所有基本类型的概述](https://dlang.org/spec/type.html)
- [_D程序设计中的_ `auto` 和 `typeof`](http://ddili.org/ders/d.en/auto_and_typeof.html)
- [类型属性](https://dlang.org/spec/property.html)
- [断言表达式](https://dlang.org/spec/expression.html#AssertExpression)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    // 大数字字面值可以用"_"分开以提高可读性。
    int b = 7_000_000;
    // 大到小转换需要 cast
    short c = cast(short) b; 
    uint d = b; // 小到大 ok
    int g;
    assert(g == 0);

    auto f = 3.1415f; // f denotes a float

    // typeid(VAR) 返回一个表达式的类型信息。
    writeln("type of f is ", typeid(f));
    double pi = f; // ok
    // 浮点数允许窄化隐式类型转换。
    float demoted = pi;

    // 访问类型属性
    assert(int.init == 0);
    assert(int.sizeof == 4);
    assert(bool.max == 1);
    writeln(int.min, " ", int.max);
    writeln(int.stringof); // int
}
```
