# Web server

Vibe.d allows writing HTTP(S) web servers in no
time:
Vibe.d能迅速编写HTTP(S)网络服务器：

    auto settings = new HTTPServerSettings;
    settings.port = 8080;
    listenHTTP(settings, &foo);

This starts a web server on port 8080 where all
requests are handled by a `foo` function:
这将在端口8080上启动一个web服务器，由`foo`函数处理他的所有请求都：

    void foo(HTTPServerRequest req,
        HTTPServerResponse res) { ... }

To ease typical usage patterns and the configuration
of different paths, a `URLRouter` class is provided
which either allows registering `GET`, `POST` etc.
handlers using the `.get("path", handler)`
and `.post("path", handler)` member functions, or
registering a custom *web interface* class which implements
web server paths as member functions:
为了简化经典使用模式和不同路径的配置，（d语言）提供了一个`URLRouter`类，它可以允许使用`.get("path", handler)`和`.post("path", handler)`成员函数来注册`GET`，`POST`等的处理程序，或注册实现Web服务器路径作为成员函数的自定义*web接口*类：

    auto router = new URLRouter;
    router.registerWebInterface(new WebService);
    listenHTTP(settings, router);

The paths of the custom web interface `WebService`'s member functions
will automatically be deduced using a simple scheme:
* `index()` will handle`/index`
* `getName()` will handle the `GET` request `/name`
* `postUsername()` will handle to `POST` request
  to `/username`
自定义Web接口`WebService`的成员函数的路径将自动使用简单的格式推导出来：
* `index()`将处理`/index`
* `getName()`将处理到`/name`的`GET`请求
* `postUsername()`将处理到`/username`的`POST`请求


Custom paths can be set by attributing a member
function with a `@path("/hello/world")` attribute.
Parameters for `POST` requests will be made available
to the function using `_` prefixed variable names. It is
also possible to specify parameters directly
in the path itself:
自定义路径可以通过赋予成员函数`@path("/hello/world")`属性来设置。在函数中可以通过在变量名前加`_`前缀来访问`POST`请求的参数。
也可以指定在路径中的参数：

    @path("/my/api/:id")
    void foo(int _id)

You don't need to pass `HTTPServerResponse` and
`HTTPServerRequest` as parameter to each function.
Vibe.d statically checks whether it is in a function's parameter list
and just passes it if needed.
你不需要将`HTTPServerResponse`和`HTTPServerRequest`作为参数传递给每个函数。
Vibe.d会静态检查它是否在函数的参数列表中，并在需要时传递它。

## {SourceCode:disabled}

```d
import vibe.d;

class WebService
{
    /*
    Using session variables such as this one,
    information associated with individual users
    can be persisted throughout all requests
    for the duration of each user's session.
    */
    private SessionVar!(string, "username")
        username_;

    /*
    By default requests to the root path ("/")
    are routed to the index method.
    */
    void index(HTTPServerResponse res)
    {
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <form action="/username" method="POST">
        Your name:
        <input type="text" name="username">
        <input type="submit" value="Submit">
        </form>
        </body>
        </html>};

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    /*
    The @path attribute can be used to customize
    url routing. Here requests to "/name"
    will be mapped to the getName method.
    */
    @path("/name")
    void getName(HTTPServerRequest req,
            HTTPServerResponse res)
    {
        import std.string : format;

        // Generate header info <li>
        // tags by inspecting the request's
        // headers property.
        string[] headers;
        foreach(key, value; req.headers) {
            headers ~=
                "<li>%s: %s</li>"
                .format(key, value);
        }
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <h1>Your name: %s</h1>
        <h2>Headers</h2>
        <ul>
        %s
        </ul>
        </body>
        </html>}.format(username_,
                headers.join("\n"));

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    void postUsername(string username,
            HTTPServerResponse res)
    {
        username_ = username;
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <h1>Your name: %s</h1>
        </body>
        </html>}.format(username_);

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }
}

void helloWorld(HTTPServerRequest req,
        HTTPServerResponse res)
{
    res.writeBody("Hello");
}

shared static this()
{
    auto router = new URLRouter;
    router.registerWebInterface(new WebService);
    router.get("/hello", &helloWorld);

    auto settings = new HTTPServerSettings;
    // Needed for SessionVar usage.
    settings.sessionStore =
        new MemorySessionStore;
    settings.port = 8080;
    listenHTTP(settings, router);
}
```
