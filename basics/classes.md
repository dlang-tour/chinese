# 类

D为像Java或C++的类和接口提供支持。

任何`class`类型都隐式地从[`Object`]（https://dlang.org/phobos/object.html）继承。

    class Foo { } // 从Object继承
    class Bar : Foo { } // Bar 也是 Foo

D中的类一般使用`new`在堆上实例化：

    auto bar = new Bar;

Class对象总是引用类型，不像`struct`会按值复制。

    Bar bar = foo; // bar 指向 foo

垃圾回收器会确保当没有其他指向这个对象的引用时释放内存。

### 继承

如果一个基类的成员函数被重载了，关键词`override`需要被指定。保护了函数的未继承的重载。

    class Bar : Foo {
        override functionFromFoo() {}
    }

在D中，类只能从一个类继承而来。

### Final 和 abstract 成员函数

- 可以在基类中标记一个函数`final`，以禁止覆盖它
- 可以声明一个函数为`abstract`来强制基类重载它
- 整个类可以被声明为`abstract`来确保它没有被实例化
- 可以用`super(..)`显式调用基类构造函数

### 检查标识

对于类对象来说, `==` 和 `!=` 操作符会比较对象的内容。
因此，与`null`比较是非法的，因为`null`没有内容。
`is`比较标识。 要判断不同的标识，可以使用`e1 !is e2`。

```d
MyClass c;
if (c == null)  // 错误
    ...
if (c is null)  // 正确
    ...
```

对于`struct`对象所有的位都会被比较，
对于其他操作数类型，标识与相等是一样的。

<!-- 这里的翻译感觉不是很能确定 -->

<!-- For `struct` objects all bits are compared,
for other operand types, identity is the same as equality. -->

### 深入

- [_D程序设计_ 中的 类](http://ddili.org/ders/d.en/class.html)
- [_D程序设计_ 中的 继承](http://ddili.org/ders/d.en/inheritance.html)
- [_D程序设计_ 中的 对象类](http://ddili.org/ders/d.en/object.html)
- [D规范中的类](https://dlang.org/spec/class.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
可以用于任意事物的花式类型...
*/
class Any {
    // protected只能被继承的类可见
    protected string type;

    this(string type) {
        this.type = type;
    }

    // 顺带一提，public是隐式的
    final string getType() {
        return type;
    }

    // 这需要被实现!
    abstract string convertToString();
}

class Integer : Any {
    // 只可以被Integer看见
    private {
        int number;
    }

    // 构造函数
    this(int number) {
        // 调用基类的构造函数
        super("integer");
        this.number = number;
    }

    // 这是隐式的。这是另一种方法来指定保护级别。
    public:

    override string convertToString() {
        import std.conv : to;
        // 瑞士军刀式的转换
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
        // 我们想要精确控制
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
        writeln("any's type = ", any.getType());
        writeln("Content = ",
            any.convertToString());
    }
}
```
