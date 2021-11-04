# DIET Templates DIET模板

To make writing web pages easier vibe.d supports
[DIET templates](https://vibed.org/templates/diet)
which is a simplified syntax to write HTML pages.
DIET is based on
[Jade templates](http://jade-lang.com/).
为了更方便地编写网页，vibe.d支持[DIET模板](https://vibed.org/templates/diet)，这是一种简化的HTML页面编写语法。
DIET基于[Jade模板](http://jade-lang.com/)。

    doctype html
    html(lang="en")
      head
        // D code is evaluated
        title #{pageTitle}
        // attributes
        script(type='text/javascript')
          if (foo) bar(1 + 5)
        // ID = body-id
        // style = the-style
        body#body-id.the-style
          h1 DIET template

The syntax is indentation-aware and the closing
tags don't have to be inserted.
语法是通过缩进感知的，并且不必插入结束标记。

All DIET templates are compiled and are held
in memory for maximum performance.
DIET templates allows using D code which is evaluated
when rendering the page. Single expressions
are enclosed in `#{ 1 + 1 }` and can be used anywhere
in the template. Whole D code lines are
prefixed with `-` on their own line:
所有的DIET模板都经过编译并保存在内存中以获得最佳性能。
DIET模板允许使用在渲染页面时执行D代码。
单个表达式包含在 `#{ 1 + 1 }` 中，并且可以在模板中的任何位置使用。
整个D代码行需要在它们自己的行上以 `-` 作为前缀：

    - foreach(title; titles)
      h1 #{title}

Complex expressions can be used that way, and
even functions may be defined that are used to
output final HTML.
可以这种方式使用复杂表达式，甚至可以定义用于最终输出HTML时的函数。

DIET templates are compiled using **CTFE**
and have to reside in the `views` folder
in a standard vibe.d project. To actually render
a DIET template use the `render` function within
an URL handler:
DIET模板使用**CTFE**进行编译，并且必须保存在在标准vibe.d项目的`views`文件夹中。要实际渲染DIET模板，需要在URL处理程序中使用`render`函数：

    void foo(HTTPServerResponse res) {
        string pageTitle = "Hello";
        int test = 10;
        res.render!("my-template.dt", pageTitle, test);
    }

All D variables available to a DIET template
are passed as template parameters to `render`.
所有在DIET模板中可用的D变量都需要作为模板参数传给`render`。

## {SourceCode:disabled}

```d
doctype html
html
  head
    title D statement test
  body
    - import std.algorithm : min;
    p Four items ahead:
    - foreach( i; 0 .. 4 )
      - auto num = i+1;
      p Item
        b= num
    p Prints 8:
    p= min(10, 2*6, 8)
```
