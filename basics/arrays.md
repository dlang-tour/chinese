# Arrays

D 语言有两种数组：**静态** 和 **动态** 的数组。
访问任何类型数组的元素都需要经过边界检查（除非编译器能够证明边界检查不是必须的）。
边界检查失败时会抛出一个 `RangeError` 来中断程序。
可以通过 `-boundschecks=off` 编译选项来禁用这类检查，以提升程序的效率。

#### 静态数组

如果静态数组在函数内声明，那么它就会被分配在堆栈中，否则他会被分配到静态内存里。静态数组的类型包括在它的类型里，它具有着固定的、编译时已知的长度：

```d
    int[8] arr;
```

`arr` 的类型为 `int[8]`。注意这里与 C/C++不同，数组的大小紧在元素类型的名称后，而非在变量名称后。

#### 动态数组

动态数组存储在堆上，它可以在运行时拓展或者缩小。通过 `new` 表达式能够创建一个给定大小的动态数组：

```d
    int size = 8; // 运行时变量
    int[] arr = new int[size];
```

`arr` 的类型为 `int[]`，这是一个 **切片**。[下一节](basics/slices)将会对切片进行更详细的讲解。

#### 数组的属性与操作

可以使用 `~` 操作符来连接两个数组，这将会创建一个新的动态数组。
可以像 `c[] = a[] + b[]` 这样对数组使用算术运算符，此时这个运算符会被应用在数组的每一个元素上。
我们也可以使用一个单一的值对数组的每个元素进行处理：

```d
    a[] *= 2; // multiple all elements by 2
    a[] %= 26; // calculate the modulo by 26 for all a's
```

这些操作可能会被编译器优化为使用特殊的指令一次进行。

动态数组和静态数组都提供 `.length` 属性。对于静态数组， `.length` 属性是只读的，
但是你可以通过修改它来动态改变动态数组的大小。属性 `.dup` 创建数组的一个拷贝。

当使用 `arr[idx]` 语法时, 特殊的符号 `$` 用来表示数组的长度。例如 `arr[$ - 1]` 引用了数组的最后一个元素，
它是 `arr[arr.length - 1]` 的较短写法。

### 练习

完成用来对信息解密的 `encrypt` 函数。文本会用 *Caesar encryption* 进行加密，
which shifts the characters in the alphabet using a certain index.
The to-be-encrypted text only contains characters in the range `a-z`,
which should make things easier.

### In-depth

- [Arrays in _Programming in D_](http://ddili.org/ders/d.en/arrays.html)
- [Array specification](https://dlang.org/spec/arrays.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Shifts every character in the
array `input` for `shift` characters.
The character range is limited to `a-z`
and the next character after z is a.

Params:
    input = array to shift
    shift = shift length for each char
Returns:
    Shifted char array
*/
char[] encrypt(char[] input, char shift)
{
    auto result = input.dup;
    // ...
    return result;
}

void main()
{
    // We will now encrypt the message with
    // Caesar encryption and a
    // shift factor of 16!
    char[] toBeEncrypted = [ 'w','e','l','c',
      'o','m','e','t','o','d',
      // The last , is okay and will just
      // be ignored!
    ];
    writeln("Before: ", toBeEncrypted);
    auto encrypted = encrypt(toBeEncrypted, 16);
    writeln("After: ", encrypted);

    // Make sure we the algorithm works
    // as expected
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```
