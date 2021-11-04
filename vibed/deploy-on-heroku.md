# Deploy on Heroku 在Heroku上部署

### Pre-requirements 事前准备

- Heroku [账号](https://signup.heroku.com/login)
- 安装了[Git](https://git-scm.com/) 
- 你的应用用dmd编译（`dub build`）没有错误

### 1 : Setup the app 安装app

Heroku needs to know how to communicate with a deployed application.
Hence a global `PORT` environment variable is provided which needs to be injected into an application and
it should bind and listen to this port.
For development a default port (here __8080__) should be set:
Heroku需要知道如何与已部署的应用进行通信。
因此提供了一个全局的`PORT`环境变量，它需要被注入到一个应用程序中，并且（应用程序）应该绑定并监听这个端口。
开发时应该设置一个默认端口（这里是__8080__）：

```d
shared static this() {
  // ...
  auto settings = new HTTPServerSettings;
  // Provide a default port in case of the $PORT variable isn't set.
  settings.port = environment.get("PORT", "8080").to!ushort;
  listenHTTP(settings, router);
}
```

Additionally create a `Procfile`, which is a text file in the root directory of the application, which explicitly declares what command
should be executed to start the app.
另外创建一个`Procfile`，它是应用程序根目录下的一个文本文件，其中明确声明应该执行什么命令来启动应用程序。

The `Procfile` in the example app looks like this:
示例应用的`Procfile`看起来像这样：

```
web: ./hello-world
```

### 2 : Prepare the app 准备应用

Before going further login to the Heroku Command Line by using the [Heroku Toolbelt](https://toolbelt.heroku.com/standalone).
在进行下一步之前， 需要使用[Heroku Toolbelt](https://toolbelt.heroku.com/standalone)登录Heroku命令行。

This provides access to the Heroku Command-Line Interface (CLI), which can be used for managing and scaling your applications and add-ons.
这提供对Heroku命令行界面（CLI）的访问，该界面可用于管理和扩展您的应用程序和附加组件。

After installing the toolbelt run the following:
安装好toolbelt后，运行以下命令：

```
$ heroku login
```

### 3 : Create the app 创建应用

Go to the [heroku dashboard](https://dashboard.heroku.com) and create a new app.
After doing this memorize the name of the created app, it will be useful later.
去到[heroku dashboard](https://dashboard.heroku.com)并创建一个新的应用程序。
这样做后，记住创建的应用程序的名称，它会在稍后用到。

Or use the Command-Line like this:
或者像这样使用命令行：

```
$ heroku create
Creating app... done, ⬢ rocky-hamlet-67506
https://rocky-hamlet-67506.herokuapp.com/ | https://git.heroku.com/rocky-hamlet-67506.git
```

The app's name here is *rocky-hamlet-67506*.
这里的应用名是*rocky-hamlet-67506*。

### Deploy using git 使用git进行部署

To deploy the app directly use `git` commands. A separate git remote endpoint should be added to which new releases can be pushed.
Thus the name of the newly created application
in the previous chapter needs to be passed as argument - here it is *rocky-hamlet-67506*.
使用`git`命令来直接部署应用程序。 需要添加一个单独的git remote endpoint以便推送新版本。
因此，需要将上一章中新创建的应用名作为参数传递 - 这里是*rocky-hamlet-67506*。

```
$ heroku git:remote -a rocky-hamlet-67506
```

Notice the remote endpoint is added to the git config:
注意remote endpoint被添加到git config中了：

```
$ git remote -v
heroku	https://git.heroku.com/rocky-hamlet-67506.git (fetch)
heroku	https://git.heroku.com/rocky-hamlet-67506.git (push)
```

### Adding the buildpack 添加buildpack

Buildpacks are responsible for generating assets or compiled code.
Buildpacks负责生成资源或编译代码。

For more information browse the [Heroku documentation](https://devcenter.heroku.com/articles/buildpacks)
更多信息请浏览[Heroku documentation](https://devcenter.heroku.com/articles/buildpacks)

For deployement the [Vibe.d buildpack](https://github.com/MartinNowak/heroku-buildpack-d) can be used:
[Vibe.d buildpack](https://github.com/MartinNowak/heroku-buildpack-d)可以被用于部署:

```
$ heroku buildpacks:set https://github.com/MartinNowak/heroku-buildpack-d
```

By default the buildpack uses the latest `dmd` compiler.
It is possible to use GDC or LDC and to choose a specific compiler versions by adding a `.d-compiler` file to your project.
默认情况下，buildpack使用最新的`dmd`编译器。
可以使用GDC或LDC向您的项目添加一个`.d-compiler`文件来选择特定的编译器版本。

Use `dmd`, `ldc`, or `gdc` to select the latest or `dmd-2.0xxx`, `ldc-1.0xxx`, or `gdc-4.9xxx` to
select a specific version of a compiler.
使用`dmd`，`ldc`或`gdc`来选择最新的或通过`dmd-2.0xxx`，`ldc-1.0xxx`或`gdc-4.9xxx`来选择一个特定版本的编译器。

### Deploy the code 部署代码

Proceed in your usual git habit and write awesome code.
你可以继续按照你通常的git习惯来写出优秀的代码。

To release a new version, just push the newest version to the Heroku endpoint.
要发布新版本，只需将最新版本推送到Heroku endpoint。

```
$ git add .
$ git commit -am "My first vibe.d release"
$ git push heroku master
Counting objects: 9, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 997 bytes, done.
Total 9 (delta 0), reused 0 (delta 0)

-----> Fetching custom git buildpack... done
-----> D (dub package manager) app detected
-----> Building libevent
-----> Building libev
-----> Downloading DMD
-----> Downloading dub package manager
-----> Setting PATH:
-----> Initializing toolchain
-----> Building app
       Running dub build ...
Building configuration "application", build type release
Running dmd (compile)...
Compiling diet template 'index.dt' (compat)...
Linking...
       Build was successful
-----> Discovering process types
       Procfile declares types -> web
-----> Compiled slug size: 3.5MB
-----> Launching... done, v4
       https://rocky-hamlet-67506.herokuapp.com/ deployed to Heroku
To git@heroku.com:rocky-hamlet-67506.git
 * [new branch]      master -> master
```

Open the app in the browser with the following command
使用以下命令在浏览器中打开应用程序:

```
$ heroku open
```

### Scaling dynos containers 缩放dyno容器

After deploying, the app is running on a web dyno.
Think of a dyno as a lightweight container that runs the command specified in the Procfile.
部署完成后，该应用程序将在Web dyno上运行。
可以将dyno看作是运行Procfile中指定命令的轻量级容器。

Using the `ps` command allows checking how many dynos are running:
使用`ps`命令可以检查多少个dyno正在运行：

```
$ heroku ps
Free dyno hours quota remaining this month: 550h 0m (100%)
For more information on dyno sleeping and how to upgrade, see:
https://devcenter.heroku.com/articles/dyno-sleeping

No dynos on ⬢ rocky-hamlet-67506
```

By default, the app is deployed on a free dyno which doesn't accept requests by default.
Free dynos will sleep after a half hour of inactivity. This causes a delay of a few seconds for the first request upon waking.
默认情况下，这个应用会部署在一个免费的dyno上，并且默认不接受请求。
免费的dynos将在半小时不活动后陷入睡眠。这导致唤醒它的第一次请求有几秒钟的延迟。

To start the dyno run the following:
运行以下命令来开始dyno的运作。

```
$ heroku ps:scale web=1
```

### See the logs 查看日志

Heroku treats logs as streams of time-ordered events aggregated from the output streams of all your app and Heroku components,
providing a single channel for all of the events.
Heroku将日志视为从所有应用程序和Heroku组件的输出流汇总的按时间排序的事件流，为所有事件提供单一通道。

```
$ heroku logs --tail
```

## More informations 更多信息

After deploying the app to Heroku you can make it more awesome by using add-ons. For example :
将应用程序部署到Heroku之后，您可以使用附加组件使其更加美妙。 例如：

- [Postgresql](https://elements.heroku.com/addons/heroku-postgresql)
- [MongoDb](https://elements.heroku.com/addons/mongohq)
- [Logging](https://elements.heroku.com/addons#logging)
- [Caching](https://elements.heroku.com/addons#caching)
- [Error and exceptions](https://elements.heroku.com/addons#errors-exceptions)
