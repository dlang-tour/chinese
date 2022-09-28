# Classes 类

D 提供类似于 Java/C++ 的类和接口。

任意 `class` 类型都隐式继承自 [`Object`](https://dlang.org/phobos/object.html)。

    class Foo { } // 继承自 Object
    class Bar : Foo { } // Bar 也是一个 Foo 

在 D 中通常使用 `new` 在堆上实例化对象：

    auto bar = new Bar;

类对象是总是一个引用类型，并且不像 `struct` 它们是不会复制值的。

    Bar bar = foo; // bar 指针指向 foo

垃圾收集器将在确保对象不在存在任何引用时释放内存。

### Inheritance 继承

当一个基类的成员函数被重写，必须显示写明 `override` 。这样可以防止无意识的重写函数。

    class Bar : Foo {
        override functionFromFoo() {}
    }

在 D 中，类只能继承自一个类（译者注：防止了 C++ 中的菱形继承）。

### Final 最终 和 abstract 抽象成员函数

- 函数可以在基类中标记为 `final` ，这样可以禁止在重写它。
- 函数可以被声明为 `abstract`，这样可以强制子类（派生类）重写它。
- 整个类可以被声明为 `abstract` ，这样可以保证它不能被实例化。
- 可以使用 `super(..)` 来显式调用基类的构造函数。

### 检查身份

对于类对象，`==` 和 `!=` 比较对象的内容，所以对比 `null` 是无效的，因为 `null` 没有内容。 `is` 关键字比较相同身份，`e1 !is e2` 比较不同身份。（译者注：即 `is` 检查指针引用）

```d
MyClass c;
if (c == null)  // 错误
    ...
if (c is null)  // ok
    ...
```

对于 `struct` 对象会比较所有的比特位。对于其他类型，检查个体与内容比较相同。

### 深入了解

- [_D 程序设计_ 类](http://ddili.org/ders/d.en/class.html)
- [_D 程序设计_ 继承](http://ddili.org/ders/d.en/inheritance.html)
- [_D 程序设计_ 对象](http://ddili.org/ders/d.en/object.html)
- [Classes in D spec](https://dlang.org/spec/class.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
一个可以用于所有东西的 nb 类型
*/
class Any {
    // protected 只能被继承的类看到
    protected string type;

    this(string type) {
        this.type = type;
    }

    // 顺便说下默认就是 public 的
    final string getType() {
        return type;
    }

    // 这个必须被实现
    abstract string convertToString();
}

class Integer : Any {
    // 只能被 Integer 看到
    private {
        int number;
    }

    // 构造函数
    this(int number) {
        // 调用基类的构造函数
        super("integer");
        this.number = number;
    }

    // 定义保护等级
    public:

    override string convertToString() {
        import std.conv : to;
        // The swiss army knife of conversion.
        return to!string(number);
    }
}

class Float : Any {
    private float number;

    this(float number) {
        super("float");
        this.number = number;
    }

    override string convertToString() {
        import std.string : format;
        // 我们需要控制位数
        return format("%.1f", number);
    }
}

void main()
{
    Any[] anys = [
        new Integer(10),
        new Float(3.1415f)
    ];

    foreach (any; anys) {
        writeln("any's type = ", 
            any.getType());
        writeln("Content = ",
            any.convertToString());
    }
}
```
