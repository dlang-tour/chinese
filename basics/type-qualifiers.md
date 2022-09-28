# Mutability 可变性

D 是静态类型的语言：变量在声明之后，类型就不能再发生变化。这样能让编译器尽早发现错误，并且在编译时强制进行限制。良好的类型安全性使得大型项目更容易维护。

D 中拥有很多类型限定符，但是最常用的还是 `const` 和 `immutable`。

### `immutable`

除了静态的类型系统，D 还提供了类型限定符（有时也成为“type constructors 类型构造函数”），它们允许对某些变量附加约束。例如，`immutable` 的变量只能够被初始化一次，之后不允许再修改：

```d
    immutable int err = 5;
    // 或者： immutable err = 5 ，err 的类型 int 会被自动推断出来
    err = 5; // 编译错误
```

因此，`immutable` 的对象能够在不进行同步操作的情况下安全的共享，因为它们从不会被修改，这也意味着 `immutable` 能够被完美的缓存。

### `const`

`const` 变量也不能够被修改，但是这个限制只适用于当前作用域。一个 `const` 指针能够指向 `immutable` 或者 `mutable` 的对象，这意味着改对象不会在当前作用于内被修改，但其他人可能在上下文中修改它。
API 常常接受 `const` 参数，以确保它们不修改输入的对象。这样他就允许用相同的方式处理可变或者不可变的数据。

```d
    void foo(const char[] s)
    {
        // 如果没有注释下面这一行，那么它就会
        // 导致编译时错误（不能修改 const 变量）
        // s[0] = 'x';

        import std.stdio : writeln;
        writeln(s);
    }

    // 因为 foo 接受的参数是 const 的，所以下面两行都能通过编译
    foo("abcd"); // 字符串是不可变的数组
    foo("abcd".dup); // .dup 返回数组的一个可变拷贝
```

`immutable` 和 `const` 都是可传递的类型限定符，这确保了一旦 `immutable` 或者 `const`被施加到一个类型上，那么它的所有子成员都将不可变。

### 深入了解

#### 基本参考

- [_D 程序设计_ 不可变](http://ddili.org/ders/d.en/const_and_immutable.html)
- [_D 程序设计_ 作用域](http://ddili.org/ders/d.en/name_space.html)

#### 进阶参考

- [常量(FAQ)](https://dlang.org/const-faq.html)
- [D 中的输入限定符](https://dlang.org/spec/const3.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    /**
     * 变量默认是可变的，允许修改它们：
     */
    int m = 100;     // 可变的
    writeln("m: ", typeof(m).stringof);
    m = 10;          // ok

    /**
     * 指向可变内存的指针：
     */
    // 指向可变内存的 const 指针是允许的：
    const int* cm = &m;
    writeln("cm: ", typeof(cm).stringof);
    // 无法通过 const 指针来修改它指向的内存：
    // *cm = 100; // 错误！

    // immutable 指针保证它指向的内存不会发生变化，所以不能指向可变的内存：
    // immutable int* im = &m; // 错误！

    /**
     * 不可变变量：
     */
    immutable v = 100;
    writeln("v: ", typeof(v).stringof);
    // v = 5; // 错误！

    // const 指针可能指向只读的内存，但是它也是只读的：
    const int* cv = &v;
    writeln("*cv: ", typeof(cv).stringof);
    // *cv = 10; // 错误！
}
```
