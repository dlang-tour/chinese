# 线程局部存储 Thread local storage

存储类`static`允许声明只初始化一次的对象。如果同样的行执行了两次，初始化将会被抛弃。
每个线程都可以获得它自己的`static`对象(*TLS - 线程局部存储*)，而不能读取或改变其他线程的`static`对象 - 即使变量名相同。
因此`static`因此，`static`允许声明一个对 *当前* 线程有全局状态的对象。

这与 比如C/C ++和Java中的 会导致同步的问题的 `static`意味着对整个应用都是全局的 不同。

这个被分配为`static`变量的值必须在编译时可以求值，而不必有运行时依赖！我们可以在运行时使用`static this()`单次构造函数来为结构体，类和模块初始化`static`变量。

    static int b = 42;
    // b 只初始化了一次!
    // 当运行不同的线程时，
    // 每个b都会看到它的"自己的" b，
    // 而不受其他线程的干扰。


此外，为了声明每个线程都可以看到并修改的“经典”全局变量，请使用与C的`static`等效的存储类`__gshared`。
这样丑陋的名字是一个友好的提醒：尽量少用它。

    __gshared int b = 50;
    // 也只会初始化一次!
    // 一个每个线程都能 读取 或者 改写（这使它非常危险） 的真正全局的b

### 深入

- [维基百科 上的 线程局部存储](https://en.wikipedia.org/wiki/Thread-local_storage)

## {SourceCode}

```d
import std.concurrency : spawn, thisTid;

void worker(bool firstTime)
{
    import std.stdio : writeln;
    // theStatic 只对当前线程是全局的。
    // 没有其他线程能存取它。
    // 注意只有第一次执行这样的时候才初始化它。
    static int threadState = 0;
    writeln("Thread ", thisTid,
        ": My state = ", threadState++);
    if (firstTime)
        worker(false);
}

void main()
{
    // 创建了5个每个都会
    // 调用worker(true,i)的线程
    for (size_t i = 0; i < 5; ++i) {
        spawn(&worker, true);
    }
}
```
