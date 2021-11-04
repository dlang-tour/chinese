# std.parallelism

`std.parallelism`模块实现了高级原语以方便并发编程。

### 并发 parallel

[`std.parallelism.parallel`]（http://dlang.org/phobos/std_parallelism.html#.parallel）允许自动分配`foreach`的循环体到不同的线程：

    // 并发地对arr进行平方
    auto arr = iota(1,100).array;
    foreach(ref i; parallel(arr)) {
        i = i*i;
    }

`parallel`在内部使用`opApply`操作符。全局`parallel`是`taskPool.parallel`的缩写，它是一个`任务池TaskPool`，它使用 *cpus数量-1* 个工作线程。创建你自己的实例来控制并行度。

请注意，`parallel`迭代的主体必须确保它不会修改其他工作单元可能访问的事物。

可选项`workingUnitSize`指定每个工作线程处理的元素数量。

### reduce

函数[`std.algorithm.iteration.reduce`](http://dlang.org/phobos/std_algorithm_iteration.html#reduce) - 从其他函数上下文中也被称为 *accumulate* 或 *foldl* - 对每个元素`x`调用函数`fun(acc，x)`，其中`acc`是上一个结果：

    // 0是"种子"
    auto sum = reduce!"a + b"(0, elements);

[`Taskpool.reduce`](http://dlang.org/phobos/std_parallelism.html#.TaskPool.reduce)
是`reduce`的并行模拟:

    // 并行算出一个range的总和，使用每个工作单元的首个元素作为种子
    auto sum = taskPool.reduce!"a + b"(nums);

`TaskPool.reduce`将range分割成并行减少的子range。一旦计算出这些结果，结果就会自行减少它们自己。

### `task()`

[`task`](http://dlang.org/phobos/std_parallelism.html#.task) 是一个可能需要更长时间或者应该在自己的工作线程中执行的函数的包装。 它可以在任务池中排队等待：

    auto t = task!read("foo.txt");
    taskPool.put(t);

或者在它自己的新线程中执行：

    t.executeInNewThread();

调用`yieldForce`可以得到一个任务的结果。在结果可用之前，它会阻塞。

    auto fileData = t.yieldForce;

### 深入

- [Parallelism in _Programming in D_](http://ddili.org/ders/d.en/parallelism.html)
- [std.parallelism](http://dlang.org/phobos/std_parallelism.html)

## {SourceCode}

```d
import std.parallelism : task,
    taskPool, TaskPool;
import std.array : array;
import std.stdio : writeln;
import std.range : iota;

string theTask()
{
    import core.thread : dur, Thread;
    Thread.sleep( dur!("seconds")(1) );
    return "Hello World";
}

void main()
{
    // taskpool with two threads
    auto myTaskPool = new TaskPool(2);
    // Stopping the task pool is important!
    scope(exit) myTaskPool.stop();

    // Start long running task
    // and do some other stuff in the mean
    // time..
    auto task = task!theTask;
    myTaskPool.put(task);

    auto arr = iota(1, 10).array;
    foreach(ref i; myTaskPool.parallel(arr)) {
        i = i*i;
    }

    writeln(arr);

    import std.algorithm.iteration : map;

    // Use reduce to calculate the sum
    // of all squares in parallel.
    auto result = taskPool.reduce!"a+b"(
        0.0, iota(100).map!"a*a");
    writeln("Sum of squares: ", result);

    // Get our result we sent to background
    // earlier.
    writeln(task.yieldForce);
}
```
