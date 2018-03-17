# 异常 Exceptions

这个教程是只关于用户的-`异常` - 系统-`错误`通常是致命的，而且 __绝不__ 应该被捕获。

### 捕获异常 Catching Exceptions

出现异常的常见情况是验证可能无效的用户输入。一旦抛出异常，堆栈将被展开，直到找到第一个匹配的异常处理程序。

```d
try
{
    readText("dummyFile");
}
catch (FileException e)
{
    // ...
}
```

（异常处理）可能会有多个catch块和一个不管是否发生错误都会执行的finally块。 通过`throw`抛出异常。

```d
try
{
    throw new StringException("You shall not pass.");
}
catch (FileException e)
{
    // ...
}
catch (StringException e)
{
    // ...
}
finally
{
    // ...
}
```

记住 [scope guard](gems/scope-guards) 常常是一个更好的 `try-finally` 模式。

### 自定义异常 Custom exceptions

可以很容易地从`Exception`继承并创建自定义异常：

```d
class UserNotFoundException : Exception
{
    this(string msg, string file = __FILE__, size_t line = __LINE__) {
        super(msg, file, line);
    }
}
throw new UserNotFoundException("D-Man is on vacation");
```

### 用`nothrow`进入安全的世界 Enter a safe world with `nothrow`

D编译器可以确保函数不会导致灾难性的副作用。这样的函数可以用`nothrow`关键字指出。D编译器静态地禁止在`nothrow`函数中抛出异常。

```d
bool lessThan(int a, int b) nothrow
{
    writeln("unsafe world"); // 输出可能会抛出异常，因此这样是禁止的
    return a < b;
}
```

请注意编译器能够自动推断模板代码的属性。

### std.exception

由于在发布模式下编译时约束会被删除，因此避免用户输入的契约编程很重要。<!-- 这里没翻好 -->为了方便，[`std.exception`](https://dlang.org/phobos/std_exception.html)提供了可以像`assert`一样使用但是不是抛出`AssertError`而是抛出`Exception`的[`enforce`](https://dlang.org/phobos/std_exception.html#enforce)

<!-- It is important to avoid contract programming for user-input as the contracts
are removed when compiled in release mode. For convenience
[`std.exception`](https://dlang.org/phobos/std_exception.html) provides
[`enforce`](https://dlang.org/phobos/std_exception.html#enforce)
that can be used like `assert`, but throws `Exception`s
instead of an `AssertError`. -->

```d
import std.exception : enforce;
float magic = 1_000_000_000;
enforce(magic + 42 - magic == 42, "Floating-point math is fun");

// 抛出自定义的异常
enforce!StringException('a' != 'A', "Case-sensitive algorithm");
```

总之在`std.exception`中还有更多。例如，当错误可能不是致命的时候，可以选择[collect](https://dlang.org/phobos/std_exception.html#collectException)它：

```d
import std.exception : collectException;
auto e = collectException(aDangerousOperation());
if (e)
    writeln("The dangerous operation failed with ", e);
```

为了测试是否在测试中引发异常，使用[`assertThrown`](https://dlang.org/phobos/std_exception.html#assertThrown).

### 深入

- [D的异常安全](https://dlang.org/exception-safe.html)
- [std.exception](https://dlang.org/phobos/std_exception.html)
- 系统级别 [core.exception](https://dlang.org/phobos/core_exception.html)
- [object.Exception](https://dlang.org/library/object/exception.html) - base class of all exceptions that are safe to catch and handle.

## {SourceCode}

```d
import std.file : FileException, readText;
import std.stdio : writeln;

void main()
{
    try
    {
        readText("dummyFile");
    }
    catch (FileException e)
    {
		writeln("Message:\n", e.msg);
		writeln("File: ", e.file);
		writeln("Line: ", e.line);
		writeln("Stack trace:\n", e.info);

		// 也可以使用默认格式化
		// writeln(e);
    }
}
```
