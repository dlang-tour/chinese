# Arrays 数组

在D语言中，数组有两种类型: **静态** 和 **动态**.
对任何类型数组的访问都需要经过边界检查（除非编译器可以证明不需要边界检查）。失败的边界检查会产生一个 `RangeError` ，它会中止应用程序。胆大的人可以通过编译器标志 `-boundschecks=off` 禁用此安全功能，从而牺牲安全为代价来提高速度。

#### 静态数组

如果在函数内定义，则静态数组存储在栈中，否则在静态内存中。他们有一个固定的，编译时已知的长度。 一个静态数组类型需要包含固定长度：

    int[8] arr;

`arr`的类型是 `int[8]` 。 注意，数组的长度声明在类型之后, 而不是像C/C++那样在变量名之后。

#### 动态数组

动态数组存储在堆上并且在运行时可以扩展或缩小。动态数组和它的长度使用表达式 `new` 来创建。

    int size = 8; // 运行时变量
    int[] arr = new int[size];

变量 `arr` 的类型是 `int[]`, 这是一个 **slice 切片**. 切片将在[下一节](basics/slices)进行更详细的解释。多维的数组可以使用 `auto arr = new int[3][3]` 语法来轻松地创建。

#### 数组运算和属性

数组可以使用 `~` 运算符连接起来，它将创建一个新的动态数组。

数学运算可以使用像 `c[] = a[] + b[]` 这样的语法应用于整个数组，这样就将 `a` 和 `b` 数组的所有元素相加，使得 `c[0] = a[0] + b[0]`、`c[1] = a[1] + b[1]` 等。 也可以使用单个值对整个数组执行操作：

    a[] *= 2; // 所有元素乘以2
    a[] %= 26; // 所有元素取模26

编译器可能会优化这些操作，以使用一次性执行操作的特殊处理器指令。

静态和动态数组都提供了属性 `.length`，它对于静态数组是只读的，但可以在动态数组的情况下用于动态改变其大小。 `.dup` 属性创建了数组的副本。

当通过 `arr[idx]` 语法索引一个数组时，一个特殊的 `$` 符号表示一个数组的长度。 例如，`arr[$ - 1]` 引用了最后一个元素，它是 `arr[arr.length - 1]` 的简写形式。

### 练习

完成函数`encrypt`来加密消息。 文本使用 *凯撒加密法* 加密，它使用特定索引移动字母表中的字符。 待加密的文本仅包含 `a-z` 范围内的字符，这应该会更容易一些。

### 深入了解

- [_D 程序设计_ 数组](http://ddili.org/ders/d.en/arrays.html)
- [D 切片](https://dlang.org/articles/d-array-article.html)
- [Array specification](https://dlang.org/spec/arrays.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
将数组 `input` 中的每个字符移动 `shift` 字符。
字符范围限制为`a-z`，z 之后的下一个字符是a。

参数：
    input = 待移动的数组
    shift = 每个字符移动的位数
返回：
    移动后的数组
*/
char[] encrypt(char[] input, char shift)
{
    auto result = input.dup;
    // TODO：移动每一个字符
    return result;
}

void main()
{
    // 我们现在将使用凯撒加密
    // 和位移数为16的条件来加密消息！
    char[] toBeEncrypted = [ 'w','e','l','c',
      'o','m','e','t','o','d',
      // 这种写法是没问题的，最后一个逗号将被忽略
    ];
    writeln("Before: ", toBeEncrypted);
    auto encrypted 
        = encrypt(toBeEncrypted, 16);
    writeln("After: ", encrypted);

    // 确保我们的算法和预期一样
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```
