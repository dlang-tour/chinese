# 在本地运行 D 程序

D 附带了一个编译器 `dmd`，一个脚本化的运行工具 `rdmd` 以及一个包管理工具 `dub`。

### DMD Compiler

命令行中，我们能通过传递 D 源文件名给 *DMD* 编译器来编译 D 程序，并创建二进制文件：

    dmd hello.d

有很多命令行选项能控制 *DMD* 编译器的行为，想要了解更多 *DMD* 的可用选项，请浏览
 [在线文档](https://dlang.org/dmd.html#switches) 或者在命令行中运行 `dmd --help`。

### 使用 `rdmd` 进行即时编译

和 *DMD* 编译器一起发布的帮助工具 `rdmd` 能够帮助您编译所有依赖并自动运行生成的应用程序：

    rdmd hello.d

在 UNIX 系统上，我们可以以 [Shebang/Hashbang](https://zh.wikipedia.org/wiki/Shebang) 行作为可执行的 D 文件的首行，这样我们就能通过执行脚本的方式来执行 D 程序了。

想要了解更多 `rdmd` 命令的可用选项，请浏览 [在线文档](https://dlang.org/rdmd.html) 或者在命令行中运行 `rdmd --help` 。

### `dub` 包管理器

D 的标准包管理器是 [`dub`](http://code.dlang.org)。安装好 `dub` 后，可以通过以下的命令来创建一个新项目：

    dub init hello

在文件夹内执行 `dub` 命令时，dub 将会获取项目的所有依赖，编译应用程序并执行它。
执行 `dub build` 命令会编译项目。

想要了解更多可用命令以及功能，请浏览 [在线文档](https://code.dlang.org/docs/commandline) 或者在命令行中运行 `dub help` 。

所有可用的 dub 包都能在 dub 的 [在线页面上](https://code.dlang.org) 找到。
