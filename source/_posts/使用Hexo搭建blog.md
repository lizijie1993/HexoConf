title: 使用Hexo搭建blog
comments: true
date: 2015-08-27 23:48:37
updated:
categories: 工具
tags: [Hexo, blog]
---

Hexo是一个逼格很高的，基于node.js的博客框架，其操作十分简单，从新建写作内容，到部署到网站，只需仅仅几个命令！本人在寻找能在Github Pages部署博客的框架过程中发现它的，在此记录平常写博客时常用的命令。

## 官网传送门
[Hexo 官网](https://hexo.io/zh-cn/)

## 安装
安装前，需要检查电脑中已安装：

* Node.js
* Git

若已经安装，那么可以通过以下一条命令，安装 Hexo：

```node.js
$ npm install -g hexo-cli
```

## 写作
Hexo写作支持markdown，这也是我写作时使用的语法。

### 新建文章
使用 `new` 指令：

```node.js
$ hexo new [layout] <title>
```

### Front-matter
该部分内容处于文章顶部 `---` 的上部，用于**描述**整篇文章。

| 参数 | 描述 | 默认值 |
| ---- | ---- | ------ |
| layout | 布局 | |
| title | 标题 | |
| date | 建立日期 |  文件建立日期 |
| updated | 更新日期 | 文件更新日期 |
| comments | 评论功能 | true |
| tags | 标签（不适用于分页） | |
| categories | 分类（不适用于分页） | |
| permalink | 覆盖文章网址 | |

对于以上 *不适用于分页* 及 `permalink` ，本人还不太清楚，之后遇到并解决问题后，再来更新。

## 生成文件
使用以下命令，生成静态文件

```node.js
$ hexo generate
```

## 部署
部署前，先安装 `hexo-deployer-git`:

```node.js
$ npm install hexo-deployer-git --save
```

然后修改_config.yml中的配置：

```node.js
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

最后以下命令，一键部署到服务器

```node.js
$ hexo deploy
```

## 服务器
在本地搭建服务器，然后以http协议打开，来在本地查看效果：  
安装 hexo-server：

```node.js
$ npm install hexo-server --save
```

启动服务器：

```node.js
$ hexo server
```

在浏览器地址中，输入 `http://localhost:4000` 即可查看。

## 结束语
本文简介了使用hexo写blog时需要用到的内容，详细内容还请参考[官方文档](https://hexo.io/zh-cn/docs/)。