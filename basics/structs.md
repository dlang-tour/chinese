# 结构体（struct）

一种在D中定义组合或自定义类型的方式是通过 `struct` 定义它们：

    struct Person {
        int age;
        int height;
        float ageXHeight;
    }

`struct`总是在栈中进行构造（除非使用`new`进行创建），且在赋值或函数调用传递参数中是**按值进行**复制的。

    auto p = Person(30, 180, 3.1415);
    auto t = p; // copy

当一个`struct`类型的新对象被创建的时候，它的成员会按照他们在`struct`中定义的顺序进行初始化。可以通过`this(...)`成员函数定义一个自定义的构造函数。如果需要避免命名冲突，当前实例可以通过`this`进行显式地存取：

    struct Person {
        this(int age, int height) {
            this.age = age;
            this.height = height;
            this.ageXHeight = cast(float)age * height;
        }
            ...

    Person p(30, 180); // 初始化
    p = Person(30, 180);  // 赋值给一个新的实例

一个`struct`可以包含任意数量的成员函数。这些都是默认为`public`并可以从外部进行存取的。它们也可以是`private`的，因此只在对同一个`struct`内的其他函数或在同一个模块的其他代码中，它们才是可调用的。

    struct Person {
        void doStuff() {
            ...
        private void privateStuff() {
            ...

    p.doStuff(); // 调用方法 doStuff
    p.privateStuff(); // 禁止的

### 常量成员函数 Const member functions

如果一个成员函数被声明为`const`，它不会被允许改变任意一个它的成员。这是编译器强制的。让一个成员函数成为`const`会使它可以被任意`const`或`immutable`对象调用，但也是对调用者确保了这个成员函数不会改变对象的状态。

### 静态成员函数 Static member functions

如果一个成员函数被声明为`static`，它将会可以不需要实例化的对象就被调用(例如， `Person.myStatic()`)，但是它不允许存取任何非`static`的成员。它只有在一个方法不需要存取其他对象成员域，但逻辑上属于同一个类的时候被使用。它也能被用于不创建一个显式的实例而提供一些功能，比如说一些单例（Singleton）设计模式的实现会使用`static`。

<!-- 我觉得这里Singleton可能需要添加一个单独的说明 -->

### 继承

注意一个`struct`不能从另一个`struct`继承而来。类型的继承只能使用我们会在未来的章节中见到的类来构建。总之，使用`alias this`或`mixins`可以简单地做到多态继承。

### 深入

- [_D程序设计_ 中的结构体](http://ddili.org/ders/d.en/struct.html)
- [结构体规范](https://dlang.org/spec/struct.html)

### 练习

给出了 `struct Vector3`, 实现下列函数并使示例应用程序成功运行：

* `length()` - 返回响亮的长度
* `dot(Vector3)` - 返回两个向量的点乘
* `toString()` - 返回这个响亮的字符串表示。
  这个函数 [`std.string.format`](https://dlang.org/phobos/std_format.html)
  返回一个 `printf` 使用的字符串-像是句法:
  `format("MyInt = %d", myInt)`。字符串将会被在下一个章节详细地解释。

## {SourceCode:incomplete}

```d
struct Vector3 {
    double x;
    double y;
    double z;

    double length() const {
        import std.math : sqrt;
        return 0.0;
    }

    // rhs 将会被复制
    double dot(Vector3 rhs) const {
        return 0.0;
    }

    /**
    返回: 用特殊格式表示的字符串。
     输出被限制为精确的一个!
    "x: 0.0 y: 0.0 z: 0.0"
    */
    string toString() const {
        import std.string : format;
        // 提示: 参考 std.format 
        //  的文档来看浮点数
        // 如何影响输出
        return format("");
    }
}

void main() {
    auto vec1 = Vector3(10, 0, 0);
    Vector3 vec2;
    vec2.x = 0;
    vec2.y = 20;
    vec2.z = 0;

    // 如果一个成员函数没有参数,
    // 调用函数的括号 () 可以被忽略
    assert(vec1.length == 10);
    assert(vec2.length == 20);

    // 测试点乘功能
    assert(vec1.dot(vec2) == 0);

    // 1 * 1 + 2 * 1 + 3 * 1
    auto vec3 = Vector3(1, 2, 3);
    assert(vec3.dot(Vector3(1, 1, 1)) == 6);

    // 1 * 3 + 2 * 2 + 3 * 1
    assert(vec3.dot(Vector3(3, 2, 1)) == 10);

    // 幸好有 toString() 我们就能
    // 使用 writeln 输出我们的向量
    import std.stdio : writeln, writefln;
    writeln("vec1 = ", vec1);
    writefln("vec2 = %s", vec2);

    // 检查字符串表示
    assert(vec1.toString() ==
        "x: 10.0 y: 0.0 z: 0.0");
    assert(vec2.toString() ==
        "x: 0.0 y: 20.0 z: 0.0");
}
```
