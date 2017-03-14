title: favicon
tags:
---

## 介绍

Favicon，即*favorites icon*，使浏览器收藏夹中的网页，不仅能显示网页的标题，还能显示这个网页特定的icon。除此之外，在一些浏览器中，tab上、搜索栏中，也能显示这个icon。

## 引入

在页面的头部，使用`<link>`标签引入。

```html
<link rel="shortcut icon" href="http://example.com/favicon.ico" type="image/vnd.microsoft.icon" />
```

图标可以通过`href`属性指向某个远程的地址，但部分老旧的浏览器不支持`<link>`标签设置favicon的地址，只会从网站的根目录`/favicon.ico`查找，因此最好是将该图标放在网站根目录，且使用`.ico`格式。

## 格式

常见格式为`.ico`，基本所有的浏览器都支持这个格式。除此之外，部分浏览器支持`.png`、`.svg`、`.gif`等，兼容性如下：

[Can I use favicon?](http://caniuse.com/#search=favicon)

## 动态favicon

[favico.js](https://github.com/ejci/favico.js)
[Tinycon](https://github.com/tommoor/tinycon)
[piecon](https://github.com/lipka/piecon)
