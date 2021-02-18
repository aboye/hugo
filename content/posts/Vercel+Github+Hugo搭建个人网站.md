---
title: "Vercel+Github+Hugo搭建个人网站"
date: 2021-02-14T15:18:09+08:00
typora-root-url: ../../static
---

相信很多人都用Github Page搭建过个人博客，但是Github屏蔽了baidu爬虫，而国内一般都使用baidu。所以我选择使用Vercel网站托管服务。

Vercel前身是Zeit，Vercel类似于GIthub Page，但功能更强大，国内访问更快（台湾节点）。最重要不影响baidu收录。下面说步骤。

注册Github账户

访问vercel.com，使用Github账户授权登录



![image-20210214154124227](/images/image-20210214154124227.png)

![image-20210214154621058](/images/image-20210214154621058.png)

选择Hugo模版，根据提示一直点击下一步直到完成。在Github账号下也会生成一个相关仓库，

在本地目录

~~~
git clone https://github.com/aboye/hugo.git
hugo new post/first.md
git add *
git commit -m 'first commit'
git push #成功后，几分钟后vercel网站托管会自动更新
~~~

