---
title: "Hugo引入MathJax支持数学公式"
date: 2021-02-18T15:02:52+08:00
---
mathjax是一个支持浏览器显示数学公式的js渲染引擎，用它来让hugo正确显示数学公式。

Hugo默认使用的markdown渲染后端是[blackfriday](https://github.com/russross/blackfriday)，也支持[mmark](https://github.com/miekg/mmark)。后者配置简单，只需在markdown文件的front matter里加入

~~~
markup: mmark
~~~

即可切换到 mmark。行内公式和行间公式一样

~~~
$$ 数学公式 $$
~~~

区别在于行间公式需要在“数学公式”前后加空格。

#### Hugo设置

只需要在主题目录themes/ananke/layouts/partials/site-footer.html加入代码

~~~
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
~~~

这样，hugo就支持书写数学公式了。