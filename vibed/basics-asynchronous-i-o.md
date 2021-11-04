# Basics & Asynchronous I/O 基础与异步I/O

Using the default build parameters the `main()`
function of a vibe.d application is specified
by a special `shared static this()` module
constructor:
使用默认的构建参数的vibe.d应用的`main()`函数被指定为一个特殊的`shared static this()`模块构造函数：

    import vibe.d;
    shared static this() {
        // Vibe.d code here
    }

A module constructor is executed
before `main()` and just run once. Vibe.d provides
its own `main()` and hides all the event loop and
housekeeping stuff from the user code.
模块构造函数在`main()`之前执行，且只执行一次。 Vibe.d提供自己的`main()`，并对用户代码隐藏所有事件循环和内务处理。

Vibe.d uses *fibers* to implement asynchronous I/O:
every time a call to socket would block - because we don't
have any data for reading for example - the currently
running fiber `yield`s its current execution
context and leaves the field for another
operation. When the data is available we just
resume execution:
Vibe.d使用*纤程*来实现异步I/O：每次调用套接字都会阻塞 -例如因为我们没有任何数据可供读取- 当前正在运行的纤程，`产生 yield`它的当前执行上下文并离开该区域去执行其它操作。当数据可用时，我们再恢复执行：

    // Might block but this is transparent.
    // If socket is ready vibe.d makes sure
    // we return here.
    line = connection.readLine();
    // Might block too
    connection.write(line);

Also the code looks like it is *synchronous* and
would block the current thread, but it doesn't!
The code looks clean and concise but it still
uses the power of asynchronous I/O allowing
thousands of connections on a single core.
另外，这段代码看起来像是*同步的(synchronous)*，并会阻止当前线程，但它不会！
代码看起来简洁而精确，但它仍然使用异步 I/O 的功能，使得可以在单个内核上进行数千个连接。

All vibe.d features make use of fiber based
asynchronous socket operations so you don't have
to worry about that one single slow MongoDB server connection
blocks your whole application.
所有vibe.d功能都使用了基于异步套接字操作的纤程，因此你不必担心单个缓慢的MongoDB服务器连接会阻塞你的整个应用。

Make sure to check the example which shows how
to implement a simple TCP based echo server.
请务必查看实现简单的基于TCP的回显服务器的示例。

## {SourceCode:disabled}

```d
import vibe.d;

shared static this()
{
    // Listen on port 8080 (TCP).
    // Each time a new connection arrives
    // it is handled by the delegate
    // specified as 2nd parameter. conn
    // is the TCP connection object to the
    // client.
    listenTCP(8080, (TCPConnection conn) {
        string line;
        conn.write("ECHO server says Hi!\r\n");
        conn.write("Type 'quit' to close.\r\n");
        while (line != "quit") {
            line = cast(string) conn.readLine();
            conn.write("ECHO: " ~ line
              ~ "\r\n");
        }

        // Just exiting the delegate here
        // will close the client's connection.
    });
}
```
