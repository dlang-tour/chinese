# Message Passing 消息传递

在D中，我们不用自己处理线程和做同步，而是可以使用*消息传递* 作为利用多核功能的手段。 线程通过*消息*进行通信，*消息*可以是任意值，来分配工作并进行线程间的同步。他们设计上不共享数据，从而避免了多线程的常见问题。

在D中所有实现消息传递的函数都可以在[`std.concurrency`](https://dlang.org/phobos/std_concurrency.html)模块中找到。`spawn`基于用户定义的函数创建一个新的 *线程 thread*：

    auto threadId = spawn(&foo, thisTid);

`thisTid`是`std.concurrency`内置的，并且引用消息传递所需的当前线程。 `spawn`将一个函数作为第一个参数，并将其它参数作为该函数的参数。

    void foo(Tid parentTid) {
        receive(
          (int i) { writeln("An ", i, " was sent!"); }
        );
        
        send(parentTid, "Done");
    }

`receive`函数就像`switch`-`case`一样，将从其他线程接收到的值根据类型分派给过去的`delegates`。

为了发送消息到特定的线程，需要使用`send`函数和线程的id

    send(threadId, 42);

可以用`receiveOnly`来接收一个指定的类型：

    string text = receiveOnly!string();
    assert(text == "Done");

`receive`族的函数会阻塞知道有东西被发送到线程的接收地。


### In-depth 深入

- [Exchanging Messages between Threads](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=5)
- [Messaging passing concurrency](http://ddili.org/ders/d.en/concurrency.html)
- [Pattern Matching with receive](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=6)
- [Multi-threaded file copying](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=7)
- [Thread Termination](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=8)
- [Out-of-Band Communication](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=9)
- [Mailbox crowding](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=10)

## {SourceCode}

```d
import std.stdio : writeln;
import std.concurrency : receive, receiveOnly,
    send, spawn, thisTid, Tid;

/*
A custom struct that is used as a message
for a little thread army.
*/
struct NumberMessage {
    int number;
    this(int i) {
        this.number = i;
    }
}

/*
Message is used as a stop sign for other
threads
*/
struct CancelMessage {
}

/// Acknowledge a CancelMessage
struct CancelAckMessage {
}

/*
The thread worker main
function which gets its parent id
passed as argument.
*/
void worker(Tid parentId)
{
    bool canceled = false;
    writeln("Starting ", thisTid, "...");

    while (!canceled) {
      receive(
        (NumberMessage m) {
          writeln("Received int: ", m.number);
        },
        (string text) {
          writeln("Received string: ", text);
        },
        (CancelMessage m) {
          writeln("Stopping ", thisTid, "...");
          send(parentId, CancelAckMessage());
          canceled = true;
        }
      );
    }
}

void main()
{
    Tid threads[];
    // Spawn 10 little worker threads.
    for (size_t i = 0; i < 10; ++i) {
        threads ~= spawn(&worker, thisTid);
    }

    // Odd threads get a number, even threads
    // a string!
    foreach(int idx, ref tid; threads) {
        import std.string : format;
        if (idx  % 2)
            send(tid, NumberMessage(idx));
        else
            send(tid, format("T=%d", idx));
    }

    // And all threads get the cancel message!
    foreach(ref tid; threads) {
        send(tid, CancelMessage());
    }

    // And we wait until all threads have
    // acknowledged their stop request
    foreach(ref tid; threads) {
        receiveOnly!CancelAckMessage;
        writeln("Received CancelAckMessage!");
    }
}
```
