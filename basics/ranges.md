# Ranges

如果 `foreach` 遭遇编译器时，

```
foreach (element; range)
{
    // 循环体...
}
```

他被内部重写为相似于如下：

```
for (auto __rangeCopy = range;
     !__rangeCopy.empty;
     __rangeCopy.popFront())
 {
    auto element = __rangeCopy.front;
    // 循环体...
}
```

如果range对象是引用类型（例如`class`），那么range将会被消耗并且不会用于随后的迭代（除非循环体在最后一次循环迭代之前打破）。如果范围对象是一个值类型，那么将会创建一个range的副本，并根据范围的定义方式，循环可能会也可能不会消耗原始range。 标准库中的大部分range都是结构体，所以`foreach`迭代通常是非破坏性的，虽然不能确保。 如果确保这个很重要，那么需要**forward** ranges。

任何一个满足下面接口的对象都称作**range**，因此这也是一个可以迭代的类：

```
    struct Range
    {
        T front() const @property;
        bool empty() const @property;
        void popFront();
    }
 ```
请注意，虽然通常将`empty`和`front`定义为`const`函数（意味着调用它们不会修改范围），但这不是必需的。

在 [`std.range`](http://dlang.org/phobos/std_range.html) 和[`std.algorithm`](http://dlang.org/phobos/std_algorithm.html) 中的函数提供了使用此接口的构建块。Range允许我们在可以轻松迭代的对象身后组合复杂的算法。此外，range允许我们创建 **惰性(lazy)** 对象，这些对象只在迭代中真正需要时才执行计算，例如当访问range中的下一个元素时。 特殊的range算法将在[D's Gems](gems/range-algorithms)部分稍后介绍。

### Exercise

完成创建`FibonacciRange`range的源代码，那会返回[斐波那契数列](https://en.wikipedia.org/wiki/Fibonacci_number)的数值。不要删除`assert`部分来自我欺骗！

### 深入

- [`std.algorithm`](http://dlang.org/phobos/std_algorithm.html)
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

struct FibonacciRange
{
    bool empty() const @property
    {
        // 所以斐波那契数列会什么时候
        // 结束？！
    }

    void popFront()
    {
    }

    int front() const @property
    {
    }
}

void main()
{
    import std.range : take;
    import std.array : array;

    FibonacciRange fib;

    // `take`会创建另一个range，其中最多有N个元素。
    // 此range为_惰性的_，并且如果真的需要会恰好遍历完原始range 
    auto fib10 = take(fib, 10);

    // 但是我们确实想要便利所有元素并
    // 将range转换成整型数组。
    int[] the10Fibs = array(fib10);

    writeln("The 10 first Fibonacci numbers: ",
        the10Fibs);
    assert(the10Fibs ==
        [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]);
}
```
