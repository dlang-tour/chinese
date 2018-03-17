# 模板 Templates

**D** 允许定义类似于C++和Java的模板函数，这也是意图定义 **泛型** 函数或类适用于与表达式一起编译在函数体内的任意类型：

    auto add(T)(T lhs, T rhs) {
        return lhs + rhs;
    }

模板参数`T`在实际函数的参数前的一组括号中定义。`T`是一个当函数实际使用`!`运算符*实例化*时 会被编译器替换的占位符：

    add!int(5, 10);
    add!float(5.0f, 10.0f);
    add!Animal(dog, cat); // 不会（通过）编译；Animal没有实现 +

### 隐式模板参数 Implicit Template Parameters

函数模板有两个参数集 - 第一个是编译时参数集第二个是运行时参数集。（非模板函数只能接受运行时参数）。如果调用函数时没有指定一个或者更多的编译时参数，编译器会尝试推导从运行时参数列表中推导出它们作为这些参数的类型。

    int a = 5; int b = 10;
    add(a, b); // T 被推导为 `int`
    float c = 5.0;
    add(a, c); // T 被推导为 `float`

### 模板属性 Template properties

一个函数可以使用`func!(T1, T2 ..)`语法在实现时指定任意数量的模板参数。模板参数可以是任意基础类型包括`string`和浮点数。

与Java中的泛型不同，D中的模板仅适用于编译时，并且会为实际调用函数时使用的特定类型集生成高度优化的代码。

当然，`struct`，`class`和`interface`类型也可以被定义为模板类型。

    struct S(T) {
        // ...
    }

### 深入

- [D模板教程](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [_D程序设计_中的模板](http://ddili.org/ders/d.en/templates.html)

#### 进阶

- [D模板规范](https://dlang.org/spec/template.html)
- [Templates Revisited](http://dlang.org/templates-revisited.html):  Walter Bright writes about how D improves upon C++ templates.
- [Variadic templates](http://dlang.org/variadic-function-templates.html): Articles about the D idiom of implementing variadic functions with variadic templates

## {SourceCode}

```d
import std.stdio : writeln;

/**
模板类允许animals的泛型实现
参数:
    noise = 要写入的字符串
*/
class Animal(string noise) {
    void makeNoise() {
        writeln(noise ~ "!");
    }
}

class Dog: Animal!("Woof") {
}

class Cat: Animal!("Meeoauw") {
}

/**
模板函数会接受类型T来实现函数makeNoise.
参数:
    animal = 可以发出声音（make noise）的对象
    n = makeNoise调用的次数。
*/
void multipleNoise(T)(T animal, int n) {
    for (int i = 0; i < n; ++i) {
        animal.makeNoise();
    }
}

void main() {
    auto dog = new Dog;
    auto cat = new Cat;
    multipleNoise(dog, 5);
    multipleNoise(cat, 5);
}
```
