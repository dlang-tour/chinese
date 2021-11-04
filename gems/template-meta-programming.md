# 模板元编程 Template meta programming


如果你曾经在C++中接触过 *template meta programming*，那么你可以放心使用D提供的工具来让你的生活更轻松。模板元编程是一种根据模板类型属性进行决策，从而根据将要实例化的类型使泛型更加灵活的技术。

### `static if` & `is`

像普通的`if`一样, `static if`可以根据在编译时可以求值的条件来编译一个代码块：

    static if(is(T == int))
        writeln("T is an int");
    static if (is(typeof(x) :  int))
        writeln("Variable x implicitely converts to int");

[`is` expression](http://wiki.dlang.org/Is_expression)是一个在编译期间可以执行条件的泛型助手

    static if(is(T == int)) { // T 是模板参数
        int x = 10;
    }

如果条件为`true`，则省略大括号 - 不会创建新范围。 `{ {` 和 `} }` 显式创建一个新块。

`static if`可以在代码中任何地方使用 - 函数中，全局作用域里或类型定义中。

### `mixin template`

无论何时你看到*boiler plate*， `mixin template`都是你的朋友：

<!-- boiler plate 一成不变的代码？下面是贬义词的感觉。 -->

    mixin template Foo(T) {
        T foo;
    }
    ...
    mixin Foo!int; // int foo available from here on.


插入其实例化点的`mixin template`可能包含任意数量的复杂表达式。如果你从C来的，向预处理器说拜拜吧！

### 模板约束 Template constraints

可以用任意数量的约束定义模板以强制一个类型必须有的属性:

    void foo(T)(T value)
      if (is(T : int)) { // foo!T 只有在T
                         // 转换为int时才是合法的
    }

约束可以在布尔表达式中进行组合，甚至可以包含在编译时执行的函数调用。 例如`std.range.primitives.isRandomAccessRange`检查一个类型是否是一个支持`[]`运算符的range。

### 深入

### Basics references

- [Tutorial to D Templates](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Conditional compilation](http://ddili.org/ders/d.en/cond_comp.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [More templates  _Programming in D_](http://ddili.org/ders/d.en/templates_more.html)
- [Mixins in  _Programming in D_](http://ddili.org/ders/d.en/mixin.html)

### Advanced references

- [Conditional compilation](https://dlang.org/spec/version.html)
- [Traits](https://dlang.org/spec/traits.html)
- [Mixin templates](https://dlang.org/spec/template-mixin.html)
- [D Templates spec](https://dlang.org/spec/template.html)

## {SourceCode}

```d
import std.traits : isFloatingPoint;
import std.uni : toUpper;
import std.string : format;
import std.stdio : writeln;

/*
一个只对数字、整数或浮点数有效的向量
*/
struct Vector3(T)
  if (is(T: real))
{
private:
    T x,y,z;

    /*
    产生getter和setter因为我们真的痛恨 boiler plate!
    
    var -> T getVAR() 和 void setVAR(T)
    */
    mixin template GetterSetter(string var) {
        // 使用 mixin 来构造函数名
        mixin("T get%s() const { return %s; }"
          .format(var.toUpper, var));

        mixin("void set%s(T v) { %s = v; }"
          .format(var.toUpper, var));
    }

    /*
    使用mixin template简单地产生getX，setX等函数
    */
    mixin GetterSetter!"x";
    mixin GetterSetter!"y";
    mixin GetterSetter!"z";

public:
    /*
    dot函数只有对浮点类型才是可用的。
    */
    static if (isFloatingPoint!T) {
        T dot(Vector3!T rhs) {
            return x * rhs.x + y * rhs.y +
                z * rhs.z;
        }
    }
}

void main()
{
    auto vec = Vector3!double(3,3,3);
    // 因为模板约束，这不会奏效！
    // Vector3!string 非法;

    auto vec2 = Vector3!double(4,4,4);
    writeln("vec dot vec2 = ", vec.dot(vec2));

    auto vecInt = Vector3!int(1,2,3);
    // doesn't have the function dot because
    // we statically enabled it only for float's
    // vecInt.dot(Vector3!int(0,0,0));

    // 产生了 getter 和 setters!
    vecInt.setX(3);
    vecInt.setZ(1);
    writeln(vecInt.getX, ",",
      vecInt.getY, ",", vecInt.getZ);
}
```
