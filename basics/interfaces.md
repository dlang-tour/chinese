# 接口 interface

D允许 技术上与`class`类型相似的`interface`，但是这些（interface的）成员函数需要被继承自`interface`的类实现。

    interface Animal {
        void makeNoise();
    }

`makeNoise`成员函数需要被`Dog`实现，因为它继承自`Animal`接口。实质上`makeNoise`表现得像是一个基类中的`abstract`成员函数。

    class Dog : Animal {
        override makeNoise() {
            ...
        }
    }

    auto dog = new Dog;
    Animal animal = dog; // 隐式转换为接口
    animal.makeNoise();

一个`类`可实现的`接口`数没有限制。但是它只能继承自 *一个* 基类。

### NVI (非虚拟接口non virtual interface) 模式

[NVI 模式](https://en.wikipedia.org/wiki/Non-virtual_interface_pattern)通过允许通用接口的 _非虚拟_ 方法来防止违反通用执行模式。 D通过允许在“接口”中定义`final`函数来禁止被覆写，从而简单地实现了NVI模式。通过重写其他`interface`函数可以强制执行特定的行为。

    interface Animal {
        void makeNoise();
        final doubleNoise() // NVI pattern
        {
            makeNoise();
            makeNoise();
        }
    }

### 深入

- [_D程序设计_中的接口](http://ddili.org/ders/d.en/interface.html)
- [D中的接口](https://dlang.org/spec/interface.html)

<!-- 这里对注释的翻译并不好 -->
## {SourceCode}

```d
import std.stdio : writeln;

interface Animal {
    /*
    虚函数
    需要被重写!
    */
    void makeNoise();

    /*
    NVI 模式. 在内部使用 makeNoise
    来自定义继承类的行为。
    
    参数: 
        n =  重复的次数
    */
    final void multipleNoise(int n) {
        for(int i = 0; i < n; ++i) {
            makeNoise();
        }
    }
}

class Dog: Animal {
    override void makeNoise() {
        writeln("Woof!");
    }
}

class Cat: Animal {
    override void makeNoise() {
        writeln("Meeoauw!");
    }
}

void main() {
    Animal dog = new Dog;
    Animal cat = new Cat;
    Animal[] animals = [dog, cat];
    foreach(animal; animals) {
        animal.multipleNoise(5);
    }
}
```
