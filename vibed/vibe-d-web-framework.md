# Vibe.d web framework Vibe.d网络框架

[Vibe.d](http://vibed.org) is a very powerful web
framework which this tour for example has been written
with. Here are some highlights of vibe.d:
[Vibe.d](http://vibed.org)是一个非常强大的Web框架，例如这个教程是以它编写的。 以下是vibe.d的一些亮点：

* Based on a fiber based approach for *asynchronous I/O*
  vibe.d allows to write high-performance HTTP(S) web servers
  and web services. Write code which looks synchronous
  but actually does the ugly asynchronous handling
  of thousands of connections in the background
  for you! See the next section for a thorough
  example.
* 基于纤程的*异步I/O(asynchronous I/O)*，vibe.d能编写高性能的HTTP(S) Web服务器和Web服务。 编写看上去同步的代码，但实际上却在后台为你做了难以异步处理的数千个连接！ 请参阅下一节以获取示例。
* An easy to use JSON and web interface generator
* 一个易用的JSON和Web界面生成器
* Out-of-the-box
  support for Redis and MongoDB make it easy to
  write backend systems that have a good performance
* Out-of-the-box
  对Redis和MongoDB的支持使编写具有良好性能的后端系统变得容易
* Generic TCP or UDP clients and servers can be
  written as well using this framework
* 通用TCP或UDP客户端和服务器也可以使用此框架编写

Note that the examples in this chapter
can't be run online because they
would require network support which is disabled
for obvious security reasons.
请注意，本章中的示例不能在线运行，因为它们需要网络支持，出于明显的安全原因而禁用。

The easiest way to create a vibe.d project is to install
`dub` and create a new project with *vibe.d* specified
as template:
创建vibe.d项目的最简单方法是安装`dub`并指定*vibe.d*为模板创建一个新的项目：

    dub init <project-name> -t vibe.d

`dub` will make sure that vibe.d is downloaded and
available for building your vibe.d based project.
`dub`将确保vibe.d被下载并可用于构建你的基于vibe.d的项目。

The book [D Web development](https://www.packtpub.com/web-development/d-web-development)
gives a thorough introduction into this great
framework.
[D Web development](https://www.packtpub.com/web-development/d-web-development)这本书对这个伟大的框架进行了全面的介绍。

