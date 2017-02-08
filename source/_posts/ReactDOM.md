title: ReactDOM
comments: true
date: 2017-01-25 23:45:01
categories: 前端
tags: react
---

[ReactDOM](https://facebook.github.io/react/docs/react-dom.html)

## 概括

`react-dom`包，提供了与DOM相关的一些方法。

* render
* unmountComponentAtNode
* findDOMNode

## render

```javascript
ReactDOM.render(
    element,
    container,
    [callback]
)
```

将React元素（可以是DOM，也可以是React组件）渲染至`container`容器内（原生DOM），并返回该React元素的引用（无状态组件的返回值为`null`）。
如果React元素已经渲染至`container`，则根据diff算法更新内容。
可选的`callback`函数在组件渲染或更新后触发。

注意：
* 初次渲染，会替换掉`container`中的内容
* 该方法不会修改`container`元素，只会修改其内容
* 该方法会返回React元素的引用（若为React组件，则返回根元素的引用），但不建议使用。建议在根元素上使用`ref`回调函数，保存该元素的引用。

## unmountComponentAtNode

```javascript
ReactDOM.unmountComponentAtNode(container)
```

从DOM中移除container内挂载的React组件，并清除它的事件和状态。若container内没有React组件，什么都不做。

返回值：有组件时，返回true；否则，返回false。

## findDOMNode()

```javascript
ReactDOM.findDOMNode(component)
```

用于查找`component`组件的原生DOM节点，返回值为该组件的根DOM节点。大部分情况建议在元素或组件上使用`ref`。

注意：
* 不能用于函数式组件
* `component`未挂载时，该方法会报错
* 当组件`component`的`render`函数返回`null`或`false`时，该方法返回`null`
