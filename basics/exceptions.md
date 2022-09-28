# Exceptions 异常

这里只针对用户异常 `Exceptions`，系统异常 `Errors` 通常是致命错误，__不应该__ 被捕获。

### 捕获异常

异常的一个常见情况是验证可能无效的用户输入。一旦抛出异常，堆栈将被展开直到匹配到一个异常处理。

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

可以有多个 `catch` 捕获块和一个 无论是否发生异常都会执行的 `finally` 语句。异常被 `throw` 关键字抛出。

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

记住， [scope guard 作用域保护](gems/scope-guards) 通常是比 `try-finally` 更好的方案。

### 自定义异常

可以很容易地继承 `Exception` 创建自定义异常。

```d
class UserNotFoundException : Exception
{
    this(string msg, string file = __FILE__, size_t line = __LINE__) {
        super(msg, file, line);
    }
}
throw new UserNotFoundException("D-Man is on vacation");
```

### 使用 `nothrow` 进入安全世界

D 编译器可以确认哪些函数不会导致灾难性的副作用。这些函数可以被 `nothrow` 关键字注释，D 编译器将在静态检查中禁止在 `nothrow` 中抛出异常。

```d
bool lessThan(int a, int b) nothrow
{
    writeln("unsafe world"); // 输出是可以抛出异常的，所以这里不能用 nothrow
    return a < b;
}
```

请注意，编译器可以自动判断判断模板代码的属性。

### std.exception

避免 `assert 断言` 是重要的，即将引入用户控制的 [contract programming](gems/contract-programming) 与 `assert` 一样会在 release 发布模式下删除。
[`std.exception`](https://dlang.org/phobos/std_exception.html) 提供了便捷的 
[`enforce`](https://dlang.org/phobos/std_exception.html#enforce)，可以像 `assert` 一样使用，但是抛出 `Exception` 而不是一个 `AssertError`。

```d
import std.exception : enforce;
float magic = 1_000_000_000;
enforce(magic + 42 - magic == 42, "Floating-point math is fun");

// 抛出一个自定义的异常
enforce!StringException('a' != 'A', "Case-sensitive algorithm");
```

在 `std.exception` 中还有很多功能，比如当异常可能不是致命的时候，可以选择使用 [collect](https://dlang.org/phobos/std_exception.html#collectException)：

```d
import std.exception : collectException;
auto e = collectException(aDangerousOperation());
if (e)
    writeln("The dangerous operation failed with ", e);
```

要测试是否抛出异常，请使用 [`assertThrown`](https://dlang.org/phobos/std_exception.html#assertThrown)。

### 深入了解

- [Exception Safety in D](https://dlang.org/exception-safe.html)
- [std.exception](https://dlang.org/phobos/std_exception.html)
- system-level [core.exception](https://dlang.org/phobos/core_exception.html)
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

        // 也可以使用默认的格式化
        // writeln(e);
    }
}
```
