# Install D locally

我们可以在 D 的官网上 [dlang.org](https://dlang.org) [下载](http://dlang.org/download.html) 并安装最新的 **DMD** （Digital Mars D）编译器：

### Windows

* [安装程序](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd-{{latest-release}}.exe)
* 或者: [档案](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.windows.7z)
* 使用 [chocolatey](https://chocolatey.org/packages/dmd) ： `choco install dmd`

### macOS

* `.dmg` [包](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.dmg)
* 或者：[档案](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.osx.tar.xz)
* 使用 [Homebrew](http://brew.sh)：`brew install dmd`

### Linux / FreeBSD / macOS

要在用户目录里快速安装 dmd，请运行： `curl -fsS https://dlang.org/install.sh | bash -s dmd`

这里提供了对于不同发行版的安装包：

* [ArchLinux](https://wiki.archlinux.org/index.php/D_(programming_language))
* [Debian/Ubuntu](http://d-apt.sourceforge.net).
* [Fedora/CentOS](http://dlang.org/download.html#dmd)
* [Gentoo](https://wiki.gentoo.org/wiki/Dlang)
* [OpenSuse](http://dlang.org/download.html#dmd)

### 其他编译器

除了 DMD 参考编译器，另外两个编译器也可以通过
[dlang.org](https://dlang.org) 的 [下载](http://dlang.org/download.html) 部分获取:

* [**GDC**](http://gdcproject.org/downloads) 使用 GCC 后端
* [**LDC**](https://github.com/ldc-developers/ldc#installation) 使用基于 LLVM 的后端

GDC 和 LDC 不总使用最新的 DMD 前端，但是会提供更好的优化，并且支持更多平台（例如 ARM）。

[更多信息](https://wiki.dlang.org/Compilers) 请参阅维基百科。
