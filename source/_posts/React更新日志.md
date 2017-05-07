title: React更新日志
comments: true
date: 2017-01-25 10:59:35
categories: 前端
tags: react
---

[Github Change Log](https://github.com/facebook/react/blob/master/CHANGELOG.md)

记录新功能、旧功能迁移、功能废弃相关内容，不记录bugfix、警告等小更新。

## 15.5.0

[Blog - React v15.5.0](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html)

### React

* 添加`React.createClass`的废弃警告，提示用户使用`create-react-class`模块替换。（注，也可以使用`class`创建组件）
* 添加`React.PropTypes`的废弃警告，提示用户使用[prop-types](https://github.com/reactjs/prop-types)模块替换，使用方法参考[Migrating from React.createClass](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html#migrating-from-react.createclass)

### React DOM

* 添加`react-dom/test-utils`，该模块暴露了React Test Utils

### React Test Renderer

* 添加`react-test-renderer/shallow`，该模块暴露了shallow renderer

### React Addons

* 不再维护，各addons迁移替代方案参考[Discontinuing support for React Addons](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html#discontinuing-support-for-react-addons)
* 废弃`react-addons-test-utils`，使用`react-dom/test-utils`和`react-test-renderer/shallow`替换

## 15.4.0

[Blog - React v15.4.0](https://facebook.github.io/react/blog/2016/11/16/react-v15.4.0.html)

### React

* 完全分离`React`和`React DOM`两个模块

### React DOM

* 声明不要手动调用 `PropTypes` 的警告

### React Perf

* React Perf启动后，你可以在chrome浏览器的**timeline**中查看各组件加载开销详情图表

### React Test Utils

* 在`input disabled onClick={foo} />` 调用 `Simulate.click()` ，`foo`现在会执行

### React Test Renderer

* `ReactTestRenderer.create()` 现在接受可选参数 `{createNodeMock: element => mock}`。现在可以在[Jest](http://facebook.github.io/jest/)的snapshot testing中mock refs了

## 15.3.2

### React DOM

* 添加HTML`playsInline`属性
* 添加HTML`as`属性

## 15.3.0

### React

* 添加`React.PureComponent`，替代`react-addons-pure-render-mixin`

### React DOM

* 添加SVG`xmlns`，`xmlnsXlink`属性
* 添加HTML`referrerPolicy`属性

### React Test Renderer

* 发布公共包，使用`npm install react-test-renderer`安装

### React TestUtils Add-on

* 实现`type`属性，用于`TestUtils.Simulate.*.`创建事件

## 15.2.0

### React

* 在警告中添加组件栈信息
* 添加`React.PropTypes.symbol`

### React DOM

* `<link>`元素添加`onLoad`处理函数
* `<source>`元素添加`onError`处理函数

### React Perf Add-on

* 添加`isRunning()`API

## 15.0.0

[Blog - React v15.0](https://facebook.github.io/react/blog/2016/04/07/react-v15.html)

### 变更日志

* 因使用`document.createElement`替代`node.innerHTML`，移除DOM中大部分`data-reactid`属性
* 文本外层不再附加一层`<span>`，可能会影响到部分css
* 使用**注释节点**渲染`null`，原本使用`<noscript>`标签渲染，会影响部分css，如`:nth-child`
* 函数式组件支持返回`null`
* 提升对SVG的支持，jsx支持所有SVG标签和属性
* `React.cloneElement()`解析`defaultProps`修复了bug：当传入`props`的属性值为`undefined`，不会应用`defaultProps`上对应的值
* `ReactPerf.getLastMeasurement()`返回值变更，不建议依赖该返回值

### 废弃

* React移除api：`findDOMNode`， `render`， `renderToString`，`renderToStaticMarkup`，`unmountComponentAtNode`，迁移至ReactDOM，ReactDOMServer
* 移除addons：`batchedUpdates`， `cloneWithProps`
* 移除react组件实例方法：`setProps`， `replaceProps`， `getDOMNode`
* 移除 `react/addons` 入口，以`react-addons-*`模块替代
* 传递 `children` 给void element（如input），现在会报错
* 不再支持从通过 `refs` 取得的dom上取react特定的属性（如 `this.refs.div.props`），建议使用 `ref` 回调函数获取

### 新的废弃（React16完全移除）

* 移除：`LinkedStateMixin`，`valueLink`。需自行通过wrapper组件替代，如[react-linked-input(https://www.npmjs.com/package/react-linked-input)
* `<input value={null} >` `null`值视作清空该input。React 0.14会忽略null值，React 15会警告，需要显示使用空字符串清空或使用`undefined`视作未传值
* `ReactPerf.printDOM()` 更名为 `ReactPerf.printOperations()`，`ReactPerf.getMeasurementsSummaryMap()` 更名为 `ReactPerf.getWasted()`