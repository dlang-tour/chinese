# Code generation (Parser)

In this example, a configuration parser is generated at compile-time.
Let's assume our program has a couple of configuration options,
summarized in a settings `struct`:
在这个例子中，配置解析器是在编译时生成的。
假设我们的程序有几个配置选项，在设置`struct`中进行了总结：

```d
struct Config
{
    int runs, port;
    string name;
}
```

While writing a parser for this struct, wouldn't be difficult, we would have to
constantly update the parser, whenever we modify the `Config` object. 
Hence, we are interested in writing a generic `parse` function that can
read arbitrary configuration options. For simplicity, `parse` will accept
a very simple format of `key1=value1,key2=value2` configuration options, but the same technique
can be used for any arbitrary configuration format. For many popular
configuration format, of course, readers already exist on the [DUB registry](https://code.dlang.org).
编写这个结构的解析器并不困难，只要我们修改`Config`对象，就必须不断更新解析器。
因此，我们对编写一个可以读取任意配置选项泛型`parse`函数很感兴趣。
为简单起见，`parse`将接受一个非常简单的`key1 = value1，key2 = value2`配置选项的格式，但同样的技术可以用于任意的配置格式。
当然，在[DUB注册表](https://code.dlang.org)中已存在许多流行的配置格式的阅读器了。


Reading the configuration 阅读配置
-------------------------

Let's assume the user has "name=dlang,port=8080" as a configuration string.
We then directly split the configuration options by comma and call `parse` with each
individual configuration setting.
After all configuration options have been parsed, the entire configuration
object is printed.
假设用户具有"name=dlang,port=8080"作为配置字符串。 然后我们直接用逗号分隔配置选项，并对每个单独的配置设置使用`parse`。
完成解析所有配置选项后，将打印整个配置对象。

Parse 解析
-----

`parse` is where the real magic happens, but first we split the given configuration option
(e.g. "name=dlang") by "=" into key ("name") and value ("dlang").
The switch statement is executed with the parsed key, but the interesting bit is that
the switch cases have been statically generated. `c.tupleof` returns a list of all members
in the `(idx, name)` format. The compiler detects that the `c.tupleof` is known at compile-time
and will unroll the foreach loop at compile-time.
Hence, `Conf.tupleof[idx].stringof` will yield the individual members of the struct object
and generate a case statement for each member.
Equally, while being in the static loop, the individual members can be accessed by their index:
`c.tupleof[idx]` and thus we can assign the respective member the parsed value from the given
configuration string. Moreover, `dropOne` is necessary, because the splitted range still
points at the key and thus `droOne.front` will return the second element.
Furthermore, `to!T` will do the actual parsing of the input string
to the respective type of the member of the configuration struct.
Finally, as the foreach loop is unrolled at compile-time a `break` would stop this loop.
However, after a configuration option has been successfully parsed, we don't want to jump
to the next case in the switch statement and thus a labeled break is used to break out the
switch statement.
`parse`是真正的神奇之处，但首先我们将给定的配置选项（比如说"name=dlang"）在"="处分成key("name") 和value("dlang")。
switch语句是通过解析的key执行的，但有趣的是，switch语句的cases是静态生成的。 `c.tupleof`返回以`(idx, name)`为格式的所有成员的列表。
如果在编译时，编译器检测到`c.tupleof`是已知的，将会在编译时展开foreach循环。
因此，`Conf.tupleof[idx].stringof`将产生结构对象的各个成员并为每个成员生成一个case语句。
同样，在静态循环中，一个成员可以通过索引来访问：`c.tupleof[idx]`，因此我们可以从给定的配置字符串中为个别成员分配解析值。
另外，`dropOne`是必须的，因为分割过的range仍然指向key，因此`droOne.front`将返回第二个元素。
此后，`to！T`会将输入字符串实际解析到配置结构成员的相应类型。
最后，由于foreach循环在编译时展开，所以`break`会停止这个循环。在配置选项成功解析后，我们不希望跳转到switch语句中的下一个case，因此使用带标签的break跳出switch语句。


## {SourceCode}

```d
import std.algorithm, std.conv, std.range,
        std.stdio, std.traits;
struct Config
{
    int runs, port;
    string name;
}
void main()
{
    Config conf;
    // use the generated parser for each entry
    "runs=1,port=2,name=hello"
        .splitter(",")
        .each!(e => conf.parse(e));
    conf.writeln;
}
void parse(Conf)(ref Conf c, string entry)
{
    auto r = entry.splitter("=");
    Switch: switch (r.front)
    {
        foreach (idx, _; c.tupleof)
        {
            alias T = typeof(Conf.tupleof[idx]);
            case Conf.tupleof[idx].stringof:
                c.tupleof[idx] = r.dropOne
                                  .front.to!T;
                break Switch;
        }
        default:
        assert (0, "Unknown member name.");
    }
}
```
