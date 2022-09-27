# Templates 模板
**D** 允许定义类似于 C++/Java 的模板函数，这是一种定义**generic 泛型**函数或对象的方式，用于编译函数体内的任何类型。

    auto add(T)(T lhs, T rhs) {
        return lhs + rhs;
    }

模板参数 `T` 在实际函数参数前面的一组圆括号内。 `T` 是一个占位符，在使用 `!` 操作实际*实例化*函数后被编译器替换：

    add!int(5, 10);
    add!float(5.0f, 10.0f);
    add!Animal(dog, cat); // 编译失败，因为 Animal 没有实现 + 操作

### 隐式模板参数

模板函数有两个参数集 - 一个用于编译期，一个用于运行时。（非模板函数只能接受运行时参数）。如果调用函数时有一个或者多个编译期参数未指定，编译器会尝试从运行时参数推断这些参数的类型。

    int a = 5; int b = 10;
    add(a, b); // T 被推断成 `int`
    float c = 5.0;
    add(a, c); // T 被推断成 `float`

### 模板属性

函数可以有任意数量的参数，这些参数在使用时用 `func!(T1, T2 ..)` 这样的语法。
模板参数可以是任意基本类型，包括 `string` 字符串和浮点数。

与 Java 中的泛型不同的是 D 中的模板是编译期的，并且生成高度优化的代码以适应调用函数的特定类型。

当然 `struct`、 `class` 和 `interface` 类型也可以定义为模板类型。

    struct S(T) {
        // ...
    }

### 深入了解

- [D 模板教程](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [_D 程序设计_ 模板](http://ddili.org/ders/d.en/templates.html)

#### 进阶

- [D Templates spec](https://dlang.org/spec/template.html)
- [Templates Revisited](http://dlang.org/templates-revisited.html):  Walter Bright（译者注：D 语言作者） 写的关于 D 如何改进 C++ 模板。
- [Variadic templates](http://dlang.org/variadic-function-templates.html): 关于可以变参模板的 D 中习惯用法。

## {SourceCode}

```d
import std.stdio : writeln;

/**
允许实现泛型 Animal 的模板类
参数:
    noise = string to write
*/
class Animal(string noise) {
    void makeNoise() {
        writeln(noise ~ "!");
    }
}

class Dog: Animal!("汪汪汪") {
}

class Cat: Animal!("喵喵喵") {
}

/**
接受实现 makeNoise 任意类型 T 的模板函数
参数:
    animal = 有 makeNoise() 的对象
    n = makeNoise 调用次数
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
