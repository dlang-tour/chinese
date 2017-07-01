# 类

D语言提供类似Java或C++那样的“类”和“接口”支持。

任何 `class` 类型隐式地继承 [`Object`](https://dlang.org/phobos/object.html)。

    class Foo { } // 继承 Object
    class Bar : Foo { } // Bar 和 Foo 相同

一般地，在D语言中，使用 `new` 会将类实例化到堆区：

    auto bar = new Bar;

类对象总是引用类型，不像 `struct` 那样进行值复制：

    Bar bar = foo; // bar 指向 foo

当指向一个对象的引用不再存在时，垃圾回收器会自动释放这部分内存。

### 继承

如果要覆盖基类的成员函数，子类的该成员函数必须使用关键字 `override` 声明，这可以防止不经意的错误覆盖。

    class Bar : Foo {
        override functionFromFoo() {}
    }

D语言的类只支持单继承。

### final 和 abstract 成员函数

- 成员函数可以被标记为 `final` 来防止被覆盖
- 成员函数可以被声明为 `abstract` 来强制子类（重新）实现它
- 声明为 `abstract` 的类不能被实例化
- `super(..)` 可以显式地调用基类的构造函数

### 判等

对于类对象， `==` 和 `!=` 运算符比较对象的内容。因此，与 `null` 比较是（无效/非法）的，因为 `null` 没有内容。
`is` 运算符可以判断相等性，要判断不等性，使用 `e1 !is e2` 。

```d
MyClass c;
if (c == null)  // 错误
    ...
if (c is null)  // ok
    ...
```

对于 `struct` 对象，进行逐位比较；对于其他普通类型，则和 `==` 是等价的。

### 深入

- [_D程序设计中的_ 类](http://ddili.org/ders/d.en/class.html)
- [_D程序设计中的_ 继承](http://ddili.org/ders/d.en/inheritance.html)
- [_D程序设计中的_ Object 类](http://ddili.org/ders/d.en/object.html)
- [D语言类规范](https://dlang.org/spec/class.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
设想一个可以用于任何事物的类...
*/
class Any {
    // protected 可以被子类访问
    protected string type;

    this(string type) {
        this.type = type;
    }

    // public 是隐式的
    final string getType() {
        return type;
    }

    // 这个方法需要实现！
    abstract string convertToString();
}

class Integer : Any {
    // 只有 Integer 可见
    private {
        int number;
    }

    // 构造函数
    this(int number) {
        // 调用基类构造函数
        super("integer");
        this.number = number;
    }

    // public 是隐式默认的
    // 这里是另一种定义保护等级的方法
    public:

    override string convertToString() {
        import std.conv : to;
        // 类型转换的瑞士军刀
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
        // 我们要控制精度
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
