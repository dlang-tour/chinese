# 子类型 Subtyping

`struct` 不能继承自其它`struct`。但是对于那些可怜的`struct`，D提供了另一种扩展功能的好方法：**subtyping**。

一个结构类型可以将其中一个成员定义为`alias this`：

    struct SafeInt {
        private int theInt;
        alias theInt this;
    }

`SafeInt`上的任何函数或操作都不能被类型本身处理，它们将被转发到已经被`alias this`的成员。从外部的`SafeInt`看起来像一个正常的整数。

这允许使用新功能扩展其他类型而在内存和运行时间方面没有开销。编译器会确保在访问`alias this`成员时做正确的事情。

`alias this`也适用于类。

## {SourceCode}

```d
import std.stdio : writeln;

struct Vector3 {
    private double[3] vec;
    alias vec this;

    double dot(Vector3 rhs) {
        return vec[0]*rhs.vec[0] +
          vec[1]*rhs.vec[1] + vec[2]*rhs.vec[2];
    }
}

void main()
{
    Vector3 vec;
    // we're basically talking with
    // double[] here.
    vec = [ 0.0, 1.0, 0.0 ];
    assert(vec.length == 3);
    assert(vec[$ - 1] == 0.0);

    auto vec2 = Vector3([1.0,0.0,0.0]);
    // but this functionality has been
    // extended!
    writeln("vec dot vec2 = ", vec.dot(vec2));
}
```
