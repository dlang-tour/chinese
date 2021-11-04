# Fibers 纤程

**纤程 Fibers**是一种以*协作*方式实现并发的方法。 类`Fiber`在模块[`core.thread`](https://dlang.org/phobos/core_thread.html)中定义。

其基本思想是当纤程无事可做或正在等待输入时，它通过调用`Fiber.yield()`*主动*放弃执行指令的可能性。它的父上下文再次获得控制权，而纤程中的状态 - 堆栈中的所有变量 - 都被保存。纤程可以恢复，并可以继续执行紧跟在`Fiber.yield()`之后的指令。 很神奇吧？ 是的。

    void foo() {
        writeln("Hello");
        Fiber.yield();
        writeln("World");
    }
    // ...
    auto f = new Fiber(&foo);
    f.call(); // Prints Hello
    f.call(); // Prints World

此功能可用于实现多个光纤协作共享单个内核的并发性。与线程相比，光纤的优势在于其资源使用率更低，因为不涉及上下文切换。

这个技术的一种非常好的使用是在[vibe.d framework](http://vibed.org)中实现非阻塞（或异步）的I/O操作中，使用纤程会得到更清爽的代码。

### In-depth 深入

- [Fibers in _Programming in D_](http://ddili.org/ders/d.en/fibers.html)
- [Documentation of core.thread.Fiber](https://dlang.org/library/core/thread/fiber.html)

## {SourceCode}

```d
import core.thread : Fiber;
import std.stdio : write;
import std.range : iota;

/**
Iterates over `range` and applies
the function `Fnc` to each element x
and returns it in `result`. Fiber yields
after each application.
*/
void fiberedRange(alias Fnc, R, T)(
    R range,
    ref T result)
{
    for(; !range.empty; range.popFront) {
        result = Fnc(range.front);
        Fiber.yield();
    }
}

void main()
{
    int squareResult, cubeResult;
    // Create a fiber that is initialized
    // with a delegate that generates a square
    // range.
    auto squareFiber = new Fiber({
        fiberedRange!(x => x*x)(
            iota(1,11), squareResult);
    });
    // .. and here is which creates cubes!
    auto cubeFiber = new Fiber({
        fiberedRange!(x => x*x*x)(
            iota(1,9), cubeResult);
    });

    // if state is TERM the fiber has finished
    // executing its associated function.
    squareFiber.call();
    cubeFiber.call();
    while (squareFiber.state
        != Fiber.State.TERM && cubeFiber.state
        != Fiber.State.TERM)
    {
        write(squareResult, " ", cubeResult);
        squareFiber.call();
        cubeFiber.call();
        write("\n");
    }

    // squareFiber could still be run because
    // it has finished yet!
}
```
