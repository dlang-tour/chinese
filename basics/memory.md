# 内存管理

D 是一门系统级编程语言，所以它允许手动内存管理。但是手动内存管理十分容易出错，所以默认情况下，D 采用 *垃圾回收器* 来管理内存。

像 C 一样，D 提供指针类型 `T*`：

```d
    int a;
    int* b = &a; // b 存储了 a 的地址
    auto c = &a; // c 的类型为 int*，并存储了 a 的地址
```

`new` 表达式会在堆上分配出一个内存块，并返回指向该托管内存的指针：

```d
    int* a = new int;
```

当 `a` 引用的内存不再被任何变量引用时，垃圾回收器就会回收这块内存。

D 有三种功能不同的安全等级： `@system`, `@trusted` 以及 `@safe`。
除非特别声明，否则安全等级默认为 `@system`。
`@safe` 是 D 的一个内存安全的子集。
`@safe` 只能够调用其他 `@safe` 或者 `@trusted` 的函数。
此外，在 `@safe` 的代码中禁止显式进行指针运算：

```d
    void main() @safe {
        int a = 5;
        int* p = &a;
        int* c = p + 5; // error
    }
```

`@trusted` 函数提供了对代码安全性手动验证的能力，这为 SafeD 与肮脏的低级世界间建立起了一座桥梁。

### 深入

* [SafeD](https://dlang.org/safed.html)

## {SourceCode}

```d
import std.stdio : writeln;

void safeFun() @safe
{
    writeln("Hello World");
    // 使用 new 分配内存是安全的
    int* p = new int;
}

void unsafeFun()
{
    int* p = new int;
    int* fiddling = p + 5;
}

void main()
{
    safeFun();
    unsafeFun();
}
```
