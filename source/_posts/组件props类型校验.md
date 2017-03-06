title: 组件props类型校验
comments: true
date: 2017-02-09 10:58:53
categories: 前端
tags: react
---

[Typechecking With PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)
[React Top-Level API](https://facebook.github.io/react/docs/react-api.html#react.proptypes)

## 概述

`React.Prototypes`输出了一组校验器，用于组件的`propTypes`对象上，检测传入组件的`props`各项类型。

```javascript
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: React.PropTypes.string
};
```

为了提升性能，类型检测仅在开发模式有效。

## 简单实例

这是一个使用不同校验器的例子：

```javascript
MyComponent.propTypes = {
  // JS原始类型
  optionalArray: React.PropTypes.array,
  optionalBool: React.PropTypes.bool,
  optionalFunc: React.PropTypes.func,
  optionalNumber: React.PropTypes.number,
  optionalObject: React.PropTypes.object,
  optionalString: React.PropTypes.string,
  optionalSymbol: React.PropTypes.symbol,

  // 任何可以被渲染的类型：数字, 字符串, 元素，或者由这些类型组成的数组或fragment
  optionalNode: React.PropTypes.node,

  // React元素
  optionalElement: React.PropTypes.element,

  // 类的实例
  // 使用JS的 instanceof 操作符
  optionalMessage: React.PropTypes.instanceOf(Message),

  // 某个确定的值
  // 这是个枚举类型
  optionalEnum: React.PropTypes.oneOf(['News', 'Photos']),

  // 任意一种类型
  optionalUnion: React.PropTypes.oneOfType([
    React.PropTypes.string,
    React.PropTypes.number,
    React.PropTypes.instanceOf(Message)
  ]),

  // 特定类型数据构成的数组
  optionalArrayOf: React.PropTypes.arrayOf(React.PropTypes.number),

  // 特定类型属性构成的对象
  optionalObjectOf: React.PropTypes.objectOf(React.PropTypes.number),

  // 特定形式的对象
  optionalObjectWithShape: React.PropTypes.shape({
    color: React.PropTypes.string,
    fontSize: React.PropTypes.number
  }),

  // 可以在以上任意校验器后链式添加`isRequired`，规定属性必填
  requiredFunc: React.PropTypes.func.isRequired,

  // 任意类型，通常会加上`isRequired`
  requiredAny: React.PropTypes.any.isRequired,

  // 自定义校验器。需要在校验失败时返回一个Error对象。
  // 不要使用`console.warn`或`throw`，因为这在`oneOfType`中无效。
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },

  // 在`arrayOf`和`objectOf`中使用自定义校验器。
  // 需要在校验失败时返回一个Error对象。在遍历数组或对象的每一项时，会调用校验器。
  // 校验器前两个参数为数组或对象本身，以及当前项的key。
  customArrayProp: React.PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};
```

## 规定单个子元素

使用`React.PropTypes.element`限制组件的`children`仅能为单个元素：

```javascript
class MyComponent extends React.Component {
  render() {
    // 必须只有一个元素，否则会发出警告
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: React.PropTypes.element.isRequired
};
```

## 默认值

设置组件的`defaultProps`，可以为`props`设置默认值：

```javascript
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 为props定义默认值:
Greeting.defaultProps = {
  name: 'Stranger'
};

// 渲染 "Hello, Stranger":
ReactDOM.render(
  <Greeting />,
  document.getElementById('example')
);
```

`propTypes`的类型检测发生在设置`defaultProps`之后，因此也会对默认值进行检测。
