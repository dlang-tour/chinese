# Interfaces 接口

D 允许定义 `interface` 接口，技术上来说像是一个 `class` 类型，但是任何继承它的类必须实现它的成员函数。

    interface IAnimal {
        void makeNoise();
    }

`makeNoise` 成员函数必须被继承自`IAnimal` 的 `Dog` 类实现。本质上 `makeNoise` 就像基类里面的 `abstract` 抽象成员函数。

    class Dog : IAnimal {
        void makeNoise() {
            ...
        }
    }

    IAnimal animal = new Dog(); // 隐式转换成接口
    animal.makeNoise();

尽管一个类只能直接继承自一个基类，但是可以实现任意数量的接口。

### NVI (non virtual interface) 非虚拟接口模式

[NVI 模式](https://en.wikipedia.org/wiki/Non-virtual_interface_pattern)
允许公共的接口是一个 _non virtual 非虚函数_。因此，该模式可以防止违反公共执行模式。
D 通过在接口中使用 `final` 函数来启用 NVI 模式。这将执行特定通过子类重写的其他抽象`interface`接口函数。

    interface IAnimal {
        void makeNoise();
        final doubleNoise() // NVI 模式
        {
            makeNoise();
            makeNoise();
        }
    }

译者注：虚函数是 C++ 中被 `virtual` 修饰的函数，通过虚函数表可以在基类指针或引用上调用子类方法，[virtual 函数说明符](https://zh.cppreference.com/w/cpp/language/virtual)，可引申为需要被子类重写的函数。这里的虚函数实际上就是指的前文的接口函数。

### 深入了解

- [_D 程序设计_ 接口](http://ddili.org/ders/d.en/interface.html)
- [Interfaces spec](https://dlang.org/spec/interface.html)

## {SourceCode}

```d
import std.stdio : writeln;

interface IAnimal {
    void makeNoise();

    /*
    NVI 模式，
    使用由继承子类重写的 makeNoise 函数

    参数:
        n =  重复次数
    */
    final void multipleNoise(int n) {
        for(int i = 0; i < n; ++i) {
            makeNoise();
        }
    }
}

class Dog: IAnimal {
    void makeNoise() {
        writeln("汪汪汪！");
    }
}

class Cat: IAnimal {
    void makeNoise() {
        writeln("喵喵喵！");
    }
}

void main() {
    IAnimal dog = new Dog;
    IAnimal cat = new Cat;
    IAnimal[] animals = [dog, cat];
    foreach(animal; animals) {
        animal.multipleNoise(5);
    }
}
```
