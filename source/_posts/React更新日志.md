title: React更新日志
comments: true
date: 2017-01-25 10:59:35
categories: 前端
tags: react
---

[Change Log](https://github.com/facebook/react/blob/master/CHANGELOG.md)

记录涉及到代码变更的更新，忽略小版本bug fix。

## 15.0.0 -> 15.4.0

* 完全分离 React 和 React DOM
* 在chrome timeline 形象地展示组件详情
* Snapshot Testing 中支持mock refs

### React

* 不再隐式包含 React DOM
* 必须的（required）`PropTypes`不能为`null`和`undefined`，缺少时给出提示
* 通过冻结`children`替换复制，提升了开发性能

### React DOM

* 修复使用React DOM时使用 浅渲染器 偶尔会出现测试失败的问题 ？
* 新增对无效的 `aria-` 属性的警告
* 新增使用`autofocus`而不是`autoFocus`的警告
* 移除垫片 `String.prototype.split` 的警告
* 阐明未手动声明 `PropTypes` 的警告 ？
* 不稳定的 `batchedUpdates` API ？
* 修复IE8下更新文本的bug

### React Perf

* 当启动 ReactPerf 时，可以在chrome timeline中看到组件渲染的相对时间花费图表

### React Test Utils

* 在`input disabled onClick={foo} />` 调用 `Simulate.click()` ，`foo`会执行（以前不会）

### React Test Renderer

* 由于打包策略变更，在同一文件中与 React DOM 一起被引入时，不会崩溃
* `ReactTestRenderer.create()` 现在接受可选参数 `{createNodeMock: element => mock}`。现在可以使用 snapshot testing 来mock refs

## 0.14.8 -> 15.0.0

### 变更日志

* 移除大部分`data-reactid`  
  因使用`document.createElement`替代`node.innerHTML`
* 文本外层不再附加一层`<span>`  
  可能会影响到部分css
* 使用 注释节点 渲染`null`，原本使用`<noscript>`标签渲染  
  会影响部分css，如`:nth-child`
* 函数式组件支持返回`null`
* 提升对SVG的支持，jsx支持所有SVG标签和属性
* `React.cloneElement()`解析`defaultProps`修复了bug：当传入`props`的属性值为`undefined`，不会应用`defaultProps`上对应的值
* `ReactPerf.getLastMeasurement()`返回值变更，不建议依赖该返回值

### 废弃

* React移除api：`findDOMNode`, `render`, `renderToString`, `renderToStaticMarkup`, `unmountComponentAtNode`，迁移至 ReactDOM, ReactDOMServer
* 移除addons：`batchedUpdates`, `cloneWithProps`
* 移除react组件实例方法：`setProps`, `replaceProps`, `getDOMNode`
* 移除 `react/addons` 入口，以 `react-addons-*` 包替代
* 传递 `children` 给void element（如input），现在会报错 ？
* 不再支持从通过 `refs` 取得的dom上取react特定的属性（如 `this.refs.div.props`，建议使用 `ref` 回调函数获取)

### 新的废弃（React16完全移除）

* 移除：`LinkedStateMixin`，`valueLink`。需自行通过wrapper组件替代，如（react-linked-input ） ？
* `<input value={null} >` `null`值视作清空该input。React 0.14会忽略null值，React 15会警告，需要显示使用空字符串清空或使用`undefined`视作未传值
* `ReactPerf.printDOM()` 更名为 `ReactPerf.printOperations()`，`ReactPerf.getMeasurementsSummaryMap()` 更名为 `ReactPerf.getWasted()`
