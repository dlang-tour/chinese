# Synchronization & Sharing 异步 & 共享

虽然在D中执行多线程的首选方法是依赖`immutable`数据并使用消息传递同步线程，但在D语言中，与类型系统一样支持一样，内置了对*synchronization*原语的支持，使用`shared`来标记可以被多个线程访问的对象。

`shared`类型标识符可以标记不同线程之间共享的变量：

    shared(int)* p = new int;
    int* t = p; // 错误

例如`std.concurrency.send`只允许发送`immutable`或`shared`数据并且会复制要发送的消息。 `shared`是可传递的，所以如果一个`class`或`struct`被标记为`shared`，那么它的所有成员都是`shared`的。
注意`static`变量默认不是`shared`，因为它们是使用*线程本地存储*（TLS）实现的，每个线程都有它自己的变量。

`synchronized` blocks allow to instruct the compiler
to create  a critical section that can only be entered
by one thread at a time.
`synchronized`块用于指示编译器创建一个关键区，同一时间只能有一个线程进入。

    synchronized {
        importStuff();
    }

在`class`成员函数中，这些块可能被用`synchronized(member1，member2)`限制为不同的成员对象的*mutexes*来减少争用。 D编译器自动插入*临界区*。一整个类也可以标记为`synchronized`，编译器将确保同一时间只有一个线程访问它的具体实例。

对`shared`变量的原子操作可以使用`core.atomic.atomicOp`助手执行：

    shared int test = 5;
    test.atomicOp!"+="(4);

### In-depth 深入

- [Data Sharing Concurrency in _Programming in D_](http://ddili.org/ders/d.en/concurrency_shared.html)
- [`shared` type qualifier](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=11)
- [Lock-Based Synchronization with `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=13)
- [Deadlocks and `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=15)
- [`synchronized` specification](https://dlang.org/spec/statement.html#SynchronizedStatement)
- [Implicit conversions with `shared` data types](https://dlang.org/spec/const3.html#implicit_conversions)

## {SourceCode}

```d
import std.concurrency : receiveOnly, send,
    spawn, Tid, thisTid;
import core.atomic : atomicOp, atomicLoad;

/*
Queue that can be used safely among
different threads. All access to an
instance is automatically locked thanks to
synchronized keyword.
*/
synchronized class SafeQueue(T)
{
    // Note: must be private in synchronized
    // classes otherwise D complains.
    private T[] elements;

    void push(T value) {
        elements ~= value;
    }

    /// Return T.init if queue empty
    T pop() {
        import std.array : empty;
        T value;
        if (elements.empty)
            return value;
        value = elements[0];
        elements = elements[1 .. $];
        return value;
    }
}

/*
Safely print messages independent of
number of concurrent threads.
Note that variadic parameters are used
for args! That is args might be 0 .. N
parameters.
*/
void safePrint(T...)(T args)
{
    // Just executed by one concurrently
    synchronized {
        import std.stdio : writeln;
        writeln(args);
    }
}

void threadProducer(shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    import std.range : iota;
    // Push values 1 to 11
    foreach (i; iota(1,11)) {
        queue.push(i);
        safePrint("Pushed ", i);
        atomicOp!"+="(*queueCounter, 1);
    }
}

void threadConsumer(Tid owner,
    shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    int popped = 0;
    while (popped != 10) {
        auto i = queue.pop();
        if (i == int.init)
            continue;
        ++popped;
        // safely fetch current value of
        // queueCounter using atomicLoad
        safePrint("Popped ", i,
            " (Consumer pushed ",
            atomicLoad(*queueCounter), ")");
    }

    // I'm done!
    owner.send(true);
}

void main()
{
    auto queue = new shared(SafeQueue!int);
    shared int counter = 0;
    spawn(&threadProducer, queue, &counter);
    auto consumer = spawn(&threadConsumer,
                    thisTid, queue, &counter);
    auto stopped = receiveOnly!bool;
    assert(stopped);
}
```
