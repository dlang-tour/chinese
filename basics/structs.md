# Structs 结构体
在 D 中定义复合类型或自定义类型的一种方法是结构体 `struct`：

    struct Person {
        int age;
        int height;
        float ageXHeight;
    }

默认情况下 `struct` 构造在栈上（除非使用 `new` 构造），并且在赋值和传参中会**复制值** 。

    auto p = Person(30, 180, 3.1415);
    auto t = p; // 发生复制

当一个新的 `struct` 类型被创建时，其成员变量可以根据定义顺序被初始化。自定义的构造函数需要被定义在叫 `this(... )` 的成员函数中。如果需要避免命名冲突，可以使用 `this` 来显式访问当前实例：

    struct Person {
        this(int age, int height) {
            this.age = age;
            this.height = height;
            this.ageXHeight = cast(float)age * height;
        }
            ...

    Person p = Person(30, 180); // 初始化
    p = Person(30, 180);  // 赋值新的实例

一个 `struct` 可以包含任意数量成员函数。默认情况下他们都是 `public` 公开的可以被外部调用。函数也可以是 `private` 私有的只能被自己的其他成员函数或同一个模块中的函数访问，

    struct Person {
        void doStuff() {
            ...
        private void privateStuff() {
            ...

    // 在其他模块中调用
    p.doStuff(); // 调用 doStuff
    p.privateStuff(); // 调用 private 不行

### Const 不可变成员函数

如果一个成员函数被声明为 `const`，那么它就不能修改它的成员。编译器会强制检查。
`const` 成员函数可以被声明为 `const` 或 `immutable` 的函数调用，并且也向调用者保证不会改变对象状态。

### Static 静态成员函数

如果一个成员函数被声明为 `static`，那么它可以在没有实例化对象时调用（例如：`Person.myStatic()`）但它不能访问其他非 `static` 成员。如果一个方法不需要访问任何对象成员字段，但在逻辑上属于同一个类，则可以使用它。
此外，它可以用来提供一些无需显式创建实例的功能，例如，一些单例设计模式实现使用`static`。

### 继承

注意 `struct`不能继承自其他的 `struct`。
可继承类型只能使用类来构建，我们将在后面的部分中看到这一点。不过，通过`alias this` 或 `mixins`，可以很容易地实现多态继承。

### 深入了解

- [_D 程序设计_ 结构体](http://ddili.org/ders/d.en/struct.html)
- [Struct specification](https://dlang.org/spec/struct.html)

### 练习

给定一个 `struct Vector3`, 实现以下函数使得实例程序成功运行：

* `length()` - 返回 vector 的长度
* `dot(Vector3)` - 返回两个 vector 的点积
* `toString()` - 返回 vector 的字符串表达。
  [`std.string.format`](https://dlang.org/phobos/std_format.html) 函数，可以使用类似于 `printf` 的语法并返回字符串：
  `format("MyInt = %d", myInt)`。 字符串将在后面的部分中详细解释。

## {SourceCode:incomplete}

```d
struct Vector3 {
    double x;
    double y;
    double z;

    double length() const {
        import std.math : sqrt;
        // TODO: 
        // 实现返回 Vector3 长度
        return 0.0;
    }

    // rhs 是一份拷贝
    double dot(Vector3 rhs) const {
        // TODO: 实现点积
        return 0.0;
    }
}

void main() {
    auto vec1 = Vector3(10, 0, 0);
    Vector3 vec2;
    vec2.x = 0;
    vec2.y = 20;
    vec2.z = 0;

    // 如果一个成员函数没有参数
    // 调用可以不需要 () 括号
    assert(vec1.length == 10);
    assert(vec2.length == 20);

    // 测试点积
    assert(vec1.dot(vec2) == 0);

    // 1 * 1 + 2 * 1 + 3 * 1
    auto vec3 = Vector3(1, 2, 3);
    assert(vec3.dot(Vector3(1, 1, 1)) == 6);

    // 1 * 3 + 2 * 2 + 3 * 1
    assert(vec3.dot(Vector3(3, 2, 1)) == 10);
}
```
