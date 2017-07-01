# 结构体

在D语言中，可以用 `struct` 定义一个复合类型或自定义类型 :

    struct Person {
        int age;
        int height;
        float ageXHeight;
    }

`struct` 对象总是被创建在栈区（除非使用 `new` 创建）并在赋值/传递参数时复制值。例如：

    auto p = Person(30, 180, 3.1415);
    auto t = p; // 复制

创建一个 `struct` 对象时，它的成员会按定义顺序被依次初始化。可以通过定义一个 `this(...)` 成员函数来建立构造函数，为了避免命名冲突，当前实例显式地使用 `this` 调用：

    struct Person {
        this(int age, int height) {
            this.age = age;
            this.height = height;
            this.ageXHeight = cast(float)age * height;
        }
            ...

    Person p(30, 180); // 依次初始化
    p = Person(30, 180);  // 使用构造函数

一个 `struct` 可以容纳任意数量的成员函数。默认情况下，成员函数被设定为 `public` 以允许从外部调用。成员函数也可以被设定为 `private` ，这样就只有同一 `struct` 下的其他成员函数或同一模块中的其他代码可以调用它。

    struct Person {
        void doStuff() {
            ...
        private void privateStuff() {
            ...

    p.doStuff(); // 调用方法doStuff
    p.privateStuff(); // 不允许

### const 成员函数

如果一个成员函数使用关键字 `const` 定义，那么该函数将不能修改对象成员，这是编译器强制要求的。
只要 `const` 成员函数在 `const` 或 `immutable` 对象上可用，这向调用者保证它不会改变对象的状态。

### 静态成员函数

如果一个成员函数使用关键字 `static` 定义，那么即使没有实例化的对象也可以调用它（如：`Person.myStatic()`），但是它不能访问任何非 `static` 的成员。这很适合不访问成员但逻辑上属于该类型的方法，也可以用作在不创建实例的情况下提供某些功能，例如，一些单例模式的实现使用 `static`。

### 继承

注意 `struct` 不能继承其他 `struct` 。富有层次的类型只能使用类（class）来定义，这个我们在后面的章节会讲到。但是，使用 `alias this` 或 `mixins` 之一可以轻松实现多重继承。

### 深入

- [_D程序设计中的_ 结构体](http://ddili.org/ders/d.en/struct.html)
- [D语言结构体规范](https://dlang.org/spec/struct.html)

### 练习

给出 `struct Vector3` ，完成下列成员函数使示例应用正常运行：

* `length()` - 返回向量的长度
* `dot(Vector3)` - 返回两向量的点积
* `toString()` - 返回向量的字符串表示
  函数 [`std.string.format`](https://dlang.org/phobos/std_format.html) 返回一个字符串，语法类似 `printf` ，例如：`format("MyInt = %d", myInt)`。字符串将在后面的章节中详细讲解。

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

    // rhs 会被复制
    double dot(Vector3 rhs) const {
        return 0.0;
    }

    /**
    返回: 特定格式的字符串表示，数字精确到十分位。
    "x: 0.0 y: 0.0 z: 0.0"
    */
    string toString() const {
        import std.string : format;
        // 提示: 查阅 std.format 文档了解如何输出浮点数。
        return format("");
    }
}

void main() {
    auto vec1 = Vector3(10, 0, 0);
    Vector3 vec2;
    vec2.x = 0;
    vec2.y = 20;
    vec2.z = 0;

    // 如果成员函数没有参数，可以省略调用时的括号
    assert(vec1.length == 10);
    assert(vec2.length == 20);

    // 测试点积计算
    assert(vec1.dot(vec2) == 0);

    // 1 * 1 + 2 * 1 + 3 * 1
    auto vec3 = Vector3(1, 2, 3);
    assert(vec3.dot(Vector3(1, 1, 1)) == 6);

    // 1 * 3 + 2 * 2 + 3 * 1
    assert(vec3.dot(Vector3(3, 2, 1)) == 10);

    // 有了 toString() 我们使用 writeln 输出向量
    import std.stdio : writeln, writefln;
    writeln("vec1 = ", vec1);
    writefln("vec2 = %s", vec2);

    // 检查字符串格式
    assert(vec1.toString() ==
        "x: 10.0 y: 0.0 z: 0.0");
    assert(vec2.toString() ==
        "x: 0.0 y: 20.0 z: 0.0");
}
```
