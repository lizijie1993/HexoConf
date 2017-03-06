title: React.Children
comments: true
date: 2017-02-09 15:10:03
categories: 前端
tags: react
---

[React Top-Level API](https://facebook.github.io/react/docs/react-api.html#react.children)

## React.Children

`React.Children`提供一系列处理`this.props.children`的工具。

### React.Children.map

```javascript
React.Children.map(children, function[(thisArg)])
```

对`children`中每个*直接*子元素调用函数，`thisArg`指向这个元素。若`children`为有序片段或数组则会遍历执行函数：不会传入容器对象到函数中。若`children`为`null`或`undefined`，则返回值为`null`或`undefined`，而非数组。

### React.Children.forEach 

```javascript
React.Children.forEach(children, function[(thisArg)])
```

类似`React.Children.map`，但是不返回数组。

### React.Children.count

```javascript
React.Children.count(children)
```

返回`children`中组件总数，与`React.Children.forEach`和`React.Children.map`回掉函数执行次数一致。

### React.Children.only

```javascript
React.Children.only(children)
```

返回`children`中唯一的子元素，否则抛出异常。

### React.Children.toArray

```javascript
React.Children.toArray(children)
```

将`children`转换成扁平的数组结构并返回，并给每个子集都会附上`key`属性。
这个方法十分实用：在render函数中操作`children`集合，特别是对`this.props.children`重新排序。

注：
`React.Children.toArray()`改变了每个子集的`key`，用来保留子集的嵌套语义。
