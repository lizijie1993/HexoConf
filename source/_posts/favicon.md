title: Favicon
categories: 前端
tags:
  - favicon
comments: true
date: 2017-03-30 13:10:28
---


## 背景

Favicon，即*favorites icon*。页面中引入Favicon，能使浏览器收藏夹中的快捷方式，不仅显示网站的标题，还显示网站管理员为这个网站制作的特定icon，让网站更有特色。在部分浏览器中，标签页上、搜索栏中，也能显示这个icon，比如：

Mac下Safari浏览器的搜索栏：

![搜索栏里的favicon](/2017/03/30/favicon/搜索栏_favicon.png)

Mac下Chrome的Tab&收藏夹：

![Tab&收藏夹中的favicon](/2017/03/30/favicon/tab&favorite_favicon.png)

通过对Favicon做一些定制，我们能做一些有趣的效果，比如：

Loading动画：

![Piecon实现动态favicon](/2017/03/30/favicon/piecon.gif)

提示当前登录账户的未读通知数：

![百度外卖运营后台favicon](/2017/03/30/favicon/运营后台favicon.JPG)

## Favicon用法

### 引入

默认情况下，会从**WEB服务器的根目录**读取`favicon.ico`文件。因此，只要在WEB服务器根目录放置了`favicon.ico`，就会被浏览器使用。

也可以在网页的头部`<head>`标签中，使用`<link>`标签指定引入：

```html
<!DOCTYPE html>
<html>
    <head>
        <link rel="shortcut icon" href="/favicon.ico" type="image/vnd.microsoft.icon" sizes="16x16" />
        <!-- ...其他资源 -->
    </head>
</html>
```

`rel`属性声明引入Favicon文件。

`href`属性声明Favicon文件的路径，可以是类似`/path/to/favicon.ico`的绝对路径，也可以是链向外部的完整URL`https://www.baidu.com/favicon.ico`。部分浏览器（如IE7）不支持完整URL方式引入。

`type`属性声明Favicon的MIME类型，[之后](#格式)会介绍。

`sizes`属性声明图片的尺寸，值为`HeightxWidth`或`any`。位图使用`HeightxWidth`，可伸缩的图（如svg）使用`any`。现在这个属性没有浏览器支持。

```html
<link rel="shortcut icon" href="/favicon.ico" type="image/vnd.microsoft.icon" sizes="16x16" />
```
```html
<link rel="shortcut icon" href="/favicon_32x32.ico" type="image/vnd.microsoft.icon" sizes="32x32" />
```
```html
<link rel="shortcut icon" href="/favicon_64x64.svg" type="image/svg+xml" sizes="64x64" />
```

### 格式

常见的favicon的格式有`.ico`、`.png`、`.svg`、`.gif`。

`.ico`文件的兼容性最好，所有浏览器都支持，也是最早支持的格式。

`.png`与`.ico`使用上基本没有差别，但市面上制作、处理`.png`的工具更多，应用更广。`.png`的兼容性不如`.ico`。

`.svg`的优势在于，高分辨率屏幕下，图片不会模糊。

`.gif`的favicon在部分浏览器下能实现动画效果。将favicon设置为如下`.gif`图：

![gif](http://p3.pstatp.com/large/96f000157a64dde1587)

在firefox中，有动态效果：

![gif_demo](/2017/03/30/favicon/gif_demo.gif)

IE9及以下（以上待测）不支持`.gif`格式，显示默认图标；chrome不支持动画，显示静态图。

### 加载顺序

所有浏览器都支持[MIME类型](#MIME类型)为`image/vnd.microsoft.icon`的`.ico`图标。若引用了不支持格式的favicon，浏览器会首先查找WEB服务器根目录下是否存在`favicon.ico`，若不存在，则显示浏览器默认图标，例如：

[Bing](http://cn.bing.com/search?q=b&src=IE-SearchBox&FORM=IE8SRC)通过`href`引入了后缀为`.ico`但实际为png格式的图片：

```html
<link href="/sa/simg/bing_p_rr_teal_min.ico" rel="shortcut icon">
```

Chrome支持png格式，打开后能正常显示：

![bing_chrome](/2017/03/30/favicon/bing_chrome.png)

IE9打开后显示浏览器默认图标：

![bing_ie9](/2017/03/30/favicon/bing_ie9.png)

详细兼容性见[图片格式兼容性](#图片格式兼容性)。

当页面中引入多个favicon时，各浏览器中加载顺序有所不同，比如：

```html
<link rel="shortcut icon" href="/favicon.ico" type="image/vnd.microsoft.icon" sizes="16x16" />
<link rel="shortcut icon" href="/favicon.png" type="image/png" />
<link rel="shortcut icon" href="/favicon-32.png" type="image/png" sizes="32x32" />
```

Chrome中显示`type="image/vnd.microsoft.icon"`的icon，而firefox中显示后出现的icon。还有更加复杂的情况，引用[弄懂Favicon](http://www.cnblogs.com/mingjixiaohui/p/5381541.html)这篇文章的内容：

> 令我沮丧的是，Chrome，Firefox，Opera 7+，和Safari 4+都支持PNG格式的favicon，但是Chrome和Safari在两种格式都提供的情况下却会使用ICO格式，而且完全无视favicon的声明顺序。另一方面，IE不支持PNG格式的favicon，但是它会忽略PNG格式的声明以及声明顺序而直接使用ICO格式的favicon。  
那些兼容PNG格式favicon的浏览器是如何确定使用哪个favicon的？Firefox和Safari会使用最后声明的那个。Chrome for Mac只会使用32×32大小的ICO格式。Chrome for Windows会首选16×16大小的ICO格式。如果在没有上述选项的情况下，两种平台的Chrome都会使用第一个声明的favicon，这点正好与Firefox和Safari相反。Chrome for Mac确实会忽略16×16的favicon而直接使用32×32的，如果在非视网膜屏的设备上就会把它缩小到16×16。Opera则完全是一种中立的态度，它会从所有声明的可用的favicon中完全随机地选择一个。我喜欢Opera这种做法。

为了保证加载正确的favicon，我们可以**根据浏览器UA，动态添加favicon**：

```javascript
var ua = (function () {
    var agent = navigator.userAgent.toLowerCase();
    // New function has access to 'agent' via closure
    return function (browser) {
        return agent.indexOf(browser) !== -1;
    };
}());

var chrome = ua('chrome');
// ...省略其他浏览器的UA判断

function insertFavicon(href, type) {
    var link = document.createElement('link');
    link.rel = 'shortcui icon';
    link.href = href;
    link.type = type;

    var head = document.getElementsByTagName('head')[0];
    head.appendChild(link);
}

// ...省略根据UA，调用insertFavicon的逻辑
```

### 动态favicon

利用`.gif`，可以让favicon“动起来”，但它仅在部分浏览器中“能动”。如果要展现与页面内容相关的信息（如上述提到的“提示当前登录账户的未读通知数”），它就无能为力了。这时候，我们可以借助javascript，来实现这些功能。很幸运，业内已经有了一些开源的库：

* [favico](https://github.com/ejci/favico.js)
* [Tinycon](https://github.com/tommoor/tinycon)
* [piecon](https://github.com/lipka/piecon)

[piecon](https://github.com/lipka/piecon)实现的动画参考了[Tinycon](https://github.com/tommoor/tinycon)，与[favico](https://github.com/ejci/favico.js)一样，都是基于Canvas，原理大致如下：

![piecon_原理图](/2017/03/30/favicon/piecon_原理图.png)

有兴趣的话，可以点击链接，查看它们的源码。

这些库的兼容性考虑[Canvas的兼容性](#canvas兼容性)以及[图片格式兼容性](#图片格式兼容性)。

## 结语

与favicon相关的还有触摸图标（[touch icons](https://developer.apple.com/library/content/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html)）和磁贴图标（[tile icons](http://blogs.msdn.com/b/ie/archive/2012/06/08/high-quality-visuals-for-pinned-sites-in-windows-8.aspx)）,这些图标在网页整体下显得并不起眼，但是却能将网页点缀得更加精致。

## 附录

### MIME类型

[IANA注册的MIME类型](https://www.iana.org/assignments/media-types/media-types.xhtml)

### 图片格式兼容性

图片来源：[Can I use favicon?](http://caniuse.com/#search=favicon)

PNG兼容性：

![png_favicon兼容性](/2017/03/30/favicon/png_favicon.png)

SVG兼容性：

![svg_favicon兼容性](/2017/03/30/favicon/svg_favicon.png)

### Canvas兼容性

图片来源：[Can I use canvas?](http://caniuse.com/#search=canvas)

![caniuse_canvas](/2017/03/30/favicon/caniuse_canvas.png)