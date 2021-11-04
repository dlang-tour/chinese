# 定语 Attributes

<!-- 此处我认为应该翻作定语，但是好像别的地方译作属性，我没法确定。因此后面都使用原本英文单词 -->

在D中，函数可以用不同的attribute描述。让我们看看两种内置的attribute和*用户定义attribute*。在第一章我们还提到了内置的`@safe`, `@system` 和 `@trusted`。

### `@property`

一个标记为`@property`的函数看起来就像外部世界的不同成员：

    struct Foo {
        @property bar() { return 10; }
        @property bar(int x) { writeln(x); }
    }
    
    Foo foo;
    writeln(foo.bar); // 实际上调用了 foo.bar()
    foo.bar = 10; // 调用了 foo.bar(10);

### `@nogc`

当D编译器遇到标为`@nogc`的函数，它会确保在函数的上下文中**没有**内存分配。`@nogc`函数只允许调用其它`@nogc`函数。


    void foo() @nogc {
      // 错误:
        auto a = new A;
    }

### 用户定义 attributes (UDAs)

D中的任何函数或类型都可以用用户定义的类型作attribute：

    struct Bar { this(int x) {} }
    
    struct Foo {
      @("Hello") {
          @Bar(10) void foo() {
            ...
          }
      }
    }

无论是内置的还是用户定义的，任意类型都可以作为函数的attribute。例子中的函数`foo()`会有(`string`类型的)`"Hello"`和(`Bar`类型以`10`为值的)`Bar`的attribute。
为了得到一个函数(或者类型)的attribute，需要使用返回[`TypeTuple`](https://dlang.org/phobos/std_typetuple.html)的内置编译器*traits*`__traits(getAttributes, Foo)`

<!-- 这个没翻好 -->
UDA通过为用户定义类型 提供 帮助编译时生成器适应特定类型 的维度，来增强泛型代码。

UDAs allow to enhance generic code by giving user-defined
types another dimension that helps compile time
generators to adapt to that specific type.

### 深入

- [_D 程序设计_ 中的 用户定义attribute](http://ddili.org/ders/d.en/uda.html)
- [D中的Attributes](https://dlang.org/spec/attribute.html)

