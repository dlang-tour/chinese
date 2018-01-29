# 数组

D语言中，数组有两种类型：**静态数组**和**动态数组**。
对这两种数组的访问都会进行边界检查（除非编译器可以证明边界检查是不必要的）。
如果检查过程发现错误，代码会抛出 `RangeError` 异常并退出程序。
为了获得更快但更危险的代码，程序员中的勇者们可以设置编译器标志 `-boundscheck=off` 
来禁用边界检查这个安全功能。

#### 静态数组

如果静态数组在函数中定义，它会被存储在栈上；否则它会被存储在静态内存中。
静态数组长度固定，而且编译时已知。静态数组的数据类型包括了数组长度：

    int[8] arr;

`arr` 的类型是 `int[8]`。
注意数组长度位于数据类型后面，与 C/C++ 不同（数组长度位于变量名后面）。

#### 动态数组

动态数组存储在堆上，在运行时可以被扩展或者缩短。
动态数组通过给定 `new` 表达式和长度来创建：

    int size = 8; // 运行时变量
    int[] arr = new int[size];

`arr` 的类型是 `int[]`, 它是一种切片。
我们将在 [下一节](basics/slices) 中详细讲解切片。
多维数组也很容易通过下面的语法创建，`auto arr = new int[3][3]`。

#### 数组操作和性质

两个数组可以用 `~` 算符来拼接，从而创建一个新的动态数组。

数学运算可以作用在整个数组上，语法类似 `c[] = a[] + b[]`。
其将两个数组的元素一一相加, `c[0] = a[0] + b[0]`, `c[1] = a[1] + b[1]`。
也可以对整个数组和单个数值进行计算：

    a[] *= 2; // 所有元素乘2
    a[] %= 26; // 所有元素对26取模

可能的话，编译器会通过处理器的特殊指令，在一个回合内完成所有运算。

静态和动态数组都提供了长度性质 `.length`。
这个性质对于静态数组来说是只读的。动态数组可以通过改变这个性质来改变数组的长度。
拷贝性质 `.dup` 可以创建一个数组的拷贝。

当以 `arr[idx]` 的形式来访问数组时，`$` 这个特殊符号被用来表示数组的长度。
`arr[$ - 1]` 来指代数组的最后一个元素，可以看作是 `arr[arr.length - 1]` 的简写。

### 练习

完成 `encrypt` 函数来对文字进行加密。使用*恺撒变换*，方法是将字母表中所有字母按一定数目进行偏移。
为了简单起见，需要被加密的文字只包含 `a-z` 之间的字母。

### 深入了解

- [数组 _Programming in D_](http://ddili.org/ders/d.en/arrays.html)
- [D 语言切片](https://dlang.org/d-array-article.html)
- [数组规范](https://dlang.org/spec/arrays.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
对 `input` 数组中的每一个字符偏移 `shift` 位。
字符在 `a-z` 之间，`z` 的下一个字符是 `a`。

参数：
    input = 待偏移的数组
    shift = 偏移的数目
返回值：
    偏移后的数组
*/
char[] encrypt(char[] input, char shift)
{
    auto result = input.dup;
    // ...
    return result;
}

void main()
{
    // 用凯撒变换加密信息，偏移数目是16
    char[] toBeEncrypted = [ 'w','e','l','c',
      'o','m','e','t','o','d',
      // 最后一个逗号会被忽略
    ];
    writeln("Before: ", toBeEncrypted);
    auto encrypted = encrypt(toBeEncrypted, 16);
    writeln("After: ", encrypted);

    // 确认算法是正确的
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```
