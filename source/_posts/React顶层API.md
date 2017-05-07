title: React顶层API
categories: 前端
tags: react
comments: true
date: 2017-05-07 23:55:09
---


[React Top-Level API
](https://facebook.github.io/react/docs/react-api.html)

## 概述

### Components
React组件让你将UI拆分成独立、可复用的片段，能独立地思考各个片段。可以通过继承`React.Component`和`React.PureComponent`两个类创建React组件。

* React.Component
* React.PureComponent

不使用ES6的`class`，则需要使用下面方法创建React组件：

* createClass()

## 创建React元素

推荐[使用JSX](https://facebook.github.io/react/docs/introducing-jsx.html)（React.createElement()语法糖），或者直接使用以下方法：

* createElement()
* createFactory()

参考[Use React Without JSX](https://facebook.github.io/react/docs/react-without-jsx.html)

## 修改元素

* cloneElement()
* isValidElement()
* React.Children

## 参考手册

### React.Component

`React.Component`是使用ES6语法定义组件类时的基类。

```javascript
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

查看[React.Component API 参考手册](https://facebook.github.io/react/docs/react-component.html)，了解更多基类的属性和方法。

### React.PureComponent

`React.PureComponent`类似`React.Component`，但实现了`shouldComponentUpdate()`，在该生命周期钩子中对`props`和`state`的浅比较，决定是否重新渲染组件。

如果你的React组件的`render`方法，在`props`和`state`相同时返回同样的结果，那么你可以使用`React.PureComponent`提升性能。

注释：
`React.PureComponent`的`shouldComponentUpdate()`中仅做浅比较。如果`state`或`prop`中包含复杂的数据结构（如属性值为数组、对象等），将可能带来错误的比较结果。仅在确定数据结构简单时继承`React.PureComponent`，或在已知深层数据变动时手动调用`forceUpdate()`更新组件。也可以考虑使用[immutable objects](https://facebook.github.io/immutable-js/)来快速比较嵌套数据。

`React.PureComponent`的`shouldComponentUpdate()`将跳过整个子组件树的`prop`更新。请确保所有子组件是*纯净*的。

### createClass()

```javascript
React.createClass(specification)
```

如果你没有使用ES6，你可能需要使用`React.createClass()`来创建React组件class。

```javascript
var Greeting = React.createClass({
  render: function() {
    return <h1>Hello, {this.props.name}</h1>;
  }
});
```

### createElement()

```javascript
React.createElement(
  type,
  [props],
  [...children]
)
```

创建并返回一个新的指定类型`type`的React元素。`type`参数可以是标签名字符串（如`’div’`或`’span’`），或者React组件的类型（class或function）。

使用JSX编写的代码，最终会转化成调用`React.createElement()`的代码，你不用显示地调用`React.createElement()`。

### cloneElement()

```javascript
React.cloneElement(
  element,
  [props],
  [...children]
)
```

克隆React并返回一个新的React元素，该元素的props为`[props]`参数与`element`的props浅合并的结果。新的`[...children]`将替换原`children`。原element上的`key`和`ref`属性将保留。

`React.cloneElement()`写法等同于：

```javascript
<element.type {...element.props} {...props}>{children}</element.type>
```

它也会保留`ref`s。这意味着，如果得到一个有`ref`的子元素，你不会不小心从你的祖先偷走它。在新元素上会附有同样的`ref`。

这个API用于替代废弃的`React.addons.cloneWithProps()`。

### createFactory()

```javascript 
React.createFactory(type)
```

返回创建指定类型`type`的React元素的函数。类似`React.createElement()`，`type`可以是标签名字符串（如`’div’`或`’span’`），或者React组件的类型（class或function）。

这个方法比较老旧，建议使用JSX或者`React.createElement()`代替。

### isValidElement()

```javascript
React.isValidElement(object)
```

检验`object`是否是React元素。返回`true`或`false`。

## React.children

见[React.Children](/2017/02/09/React-Children/)