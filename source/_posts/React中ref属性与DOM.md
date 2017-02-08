title: React中ref属性与DOM
comments: true
date: 2017-02-08 10:41:08
categories: 前端
tags: react
---

[Refs and the DOM](https://facebook.github.io/react/docs/refs-and-the-dom.html)

典型的React数据流中，父组件通过`props`与子组件通信。但有时父组件需要在这个数据流之外与子组件通信，可能是修改子组件实例或者DOM。`refs`属性提供了这样的途径。比如以下场景：

* 管理焦点，文本选择，媒体播放
* 触发动画
* 集成第三方DOM库

## Ref回调属性
`ref`属性可以附加在任何组件上，接受一个函数作为其值，该函数在组件`mounted`或`unmounted`后立即执行，参数为该组件实例的引用。当`ref`属性附在DOM元素上，回调函数参数为该DOM元素的引用。

```javascript
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.focus = this.focus.bind(this);
  }

  focus() {
    // Explicitly focus the text input using the raw DOM API
    this.textInput.focus();
  }

  render() {
    // Use the `ref` callback to store a reference to the text input DOM
    // element in this.textInput.
    return (
      <div>
        <input
          type="text"
          ref={(input) => { this.textInput = input; }} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focus}
        />
      </div>
    );
  }
}
```

组件`mounted`时，回调函数参数为DOM的引用；组件`unmounted`时，回调函数参数为`null`。

老的API中，`ref`属性值为字符串，并通过`this.refs.*`进行引用，但这样有些问题，未来会移除这个功能，建议使用回调函数。

`ref`属性不能用于函数式组件，因为函数式组件不存在实例，只能在函数式组件内部使用，如下：

```javascript
function CustomTextInput(props) {
  // textInput must be declared here so the ref callback can refer to it
  let textInput = null;

  function handleClick() {
    textInput.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={(input) => { textInput = input; }} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );  
}
```

## 警告
回调函数为内联函数时，组件`update`时会执行两次，第一次引用为`null`，第二次为该实例的引用。