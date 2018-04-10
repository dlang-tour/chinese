# Bit manipulation 位操作

位操作是D语言在编译期间用mixin产生代码的极好例子

### Simple bit manipulation 简单位操作

D offers the following operators for bit manipulation:
D 对位操作使用了以下操作：

- `&` 按位与
- `|` 按位或
- `~` 按位取反
- `<<`  有符号向左移位
- `>>`  有符号向右移位（保留最高位比特的符号）
- `>>>` 无符号向右移位

### A practical example 实践例子

读取一个位的值是位操作的常见示例。D为大多数常见目标提供`core.bitop.bt`，然而为了适应位操作，让我们从测试一个比特的详细实现开始：

```d
enum posA = 1;
enum maskA = (1 << posA);
bool getFieldA()
{
    return _data & maskA;
}
```

一般是测试长度大于1的块。因此需要一个特殊的读取掩码来标识块的长度，并且在应用掩码之前相应地移动数据块：

```d
enum posA = 1;
enum lenA = 3;
enum maskA = (1 << lenA) - 1; // ...0111
uint getFieldA()
{
    return (_data >> posA) & maskA;
}
```

设置这样的块可以等效地通过对掩码取反来定义，因此只允许在指定的块内写入：

```d
void setFieldA(bool b);
{
    return (_data & ~maskAWrite) | ((b << aPos) & maskAWrite);
}
```

## `std.bitmanip` to the rescue


编写一个自定义位操作代码非常有趣，而且D提供了完整的工具来做这些。 然而，在大多数情况下，我们不想复制和粘贴这样的位操作代码，因为这很容易出错并且很难维护。
因此，在D语言中，`std.bitmanip`可以帮助您编写可维护，易于阅读的位操作，而因为强大的mixin这样不会牺牲性能。

看看练习部分。 定义了一个`BitVector`，但它仍然只使用X个比特，并且与常规结构几乎没有区别。

`std.bitmanip`和`core.bitop`包含更多对于需要低内存消耗的应用程序非常有用的帮手。

### Padding and alignment 填充和对齐

因为编译器会为尺寸小于当前OS内存布局（`size_t.sizeof`）的变量添加填充，例如 `bool`，`byte`，`char`，所以建议先从高度对齐的字段开始。

## In-depth 深入

- [std.bitmanip](http://dlang.org/phobos/std_bitmanip.html) - Bit-level manipulation facilities
- [_Bit Packing like a Madman_](http://dconf.org/2016/talks/sechet.html)

## {SourceCode}

```d
struct BitVector
{
    import std.bitmanip : bitfields;
    // 使用以下代理创建私有域
    mixin(bitfields!(
        uint, "x",    2,
        int,  "y",    3,
        uint, "z",    2,
        bool, "flag", 1));
}

void main()
{
    import std.stdio : writefln, writeln;

    BitVector vec;
    vec.x = 2;
    vec.z = vec.x - 1;
    writefln("x: %d, y: %d, z: %d",
              vec.x, vec.y, vec.z);

    // 只用了8 比特 - 1 字节
    writeln(BitVector.sizeof);

    struct Vector { int x, y, z; }
    // 每个变量4个字节(int)
    writeln(Vector.sizeof);

	struct BadVector
	{
		bool a;
		int x, y, z;
		bool b;
	}
	// 因为填充，
	// 每个域都用了4个字节
	writeln(BadVector.sizeof);
}
```
