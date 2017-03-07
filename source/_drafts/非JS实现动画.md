title: 非JS实现动画
categories: 前端
tags: [svg, css3]
---

前端实现动画的手段非常丰富，有Canvas，JS+DOM，CSS3，SVG等。本文以近期项目中的动画为例，介绍CSS3和SVG这2种非JS实现动画的方式——CSS3和SVG。

## 动画拆解

以其中一个小球为例，动画可拆解为两部分：

1. 小球自身属性的变化：距离越远，半径越小、亮度越低；
2. 小球沿着轨迹的运动：围绕中心公转。

我们暂定一轮动画持续时间为10s，介绍如何实现并组合这两部分动画。

## 小球自身属性的变化

### CSS3

首先需要画一个圆，利用`border-radius`属性，很容易就能实现：

```html
<div class="circle"></div>
```

```css
.circle {
  background-color: #fff;
  height: 30px;
  width: 30px;
  border-radius: 15px;
}
```

接着，定义CSS3动画，实现半径与透明度的变化：

```css
.planet-animation {
    animation-name: planetAnimation;
    animation-duration: 10s;
    animation-timing-function: linear;
    animation-delay: 0;
    animation-iteration-count: infinite;
    animation-direction: normal; 
}

@keyframes planetAnimation {
  0% { 
    opacity: 1;
    width: 30px;
    height: 30px;
    border-radius: 15px;
  }
  50% {
    opacity: .5;
    width: 12px;
    height: 12px;
    border-radius: 6px;
  }
  100% { 
    opacity: 1;
    width: 30px;
    height: 30px;
    border-radius: 15px;
  }
}
```

最后将动画应用于小球上：

```html
<div class="circle planet-animation"></div>
```

详情可见：

<p data-height="265" data-theme-id="0" data-slug-hash="zZKdWr" data-default-tab="css,result" data-user="lizijie1993" data-embed-version="2" data-pen-title="zZKdWr" class="codepen">See the Pen <a href="http://codepen.io/lizijie1993/pen/zZKdWr/">zZKdWr</a> by lizijie (<a href="http://codepen.io/lizijie1993">@lizijie1993</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

### SVG

同样的，首先画一个圆：

```html
<svg width="1900px" height="900px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <circle cx="950" cy="210" r="15" fill="rgba(255, 255, 255, 1)">
    </circle>
</svg>
```

接下来添加动画：

```html
<svg width="1900px" height="900px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <circle cx="950" cy="210" r="15" fill="rgba(255, 255, 255, 1)">
        <animate attributeName="opacity" values="0.5;1;0.5;0.5" begin="0s" dur="10s" repeatCount="indefinite" keytimes="0;0.5;1;1"></animate>
        <animate attributeName="r" values="6;15;6;6" begin="0s" dur="10s" repeatCount="indefinite" keytimes="0;0.5;1;1"></animate>
    </circle>
</svg>
```

详情可见：

<p data-height="265" data-theme-id="0" data-slug-hash="WpGEBB" data-default-tab="html,result" data-user="lizijie1993" data-embed-version="2" data-pen-title="WpGEBB" class="codepen">See the Pen <a href="http://codepen.io/lizijie1993/pen/WpGEBB/">WpGEBB</a> by lizijie (<a href="http://codepen.io/lizijie1993">@lizijie1993</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

### 对比

相比较而言，我认为svg有以下两点优势：

1. 代码更加纯粹，只需要编写对应的标签及属性，无需编写CSS；
2. 动画更加流畅。

为什么？

## 小球沿着轨迹的运动