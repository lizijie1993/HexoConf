title: React中的Controlled Components & Uncontrolled Components
comments: true
date: 2017-02-08 10:23:40
categories: 前端
tags: react
---

[Controlled Components](https://facebook.github.io/react/docs/forms.html#controlled-components)
[Uncontrolled Components](https://facebook.github.io/react/docs/uncontrolled-components.html)

## Controlled Component

将React组件的`state`与表单的状态绑定，使`state`成为表单状态值的“唯一可信来源”，只能通过组件的`setState()`方法修改，如下：

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

## Uncontrolled Components

与Controlled Component不同，Uncontrolled Component使用[ref属性](/2017/02/08/React中ref属性与DOM/)，获取原生DOM引用，再操作DOM上的值。例如：

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

### 默认值
React组件中，设置表单`value`属性值会覆盖DOM的`value`值（无法更改，除非用Controlled Components修改绑定的`state`），因此不能使用`value`属性，需使用`defaultValue`属性，如下

```javascript
render() {
  return (
    <form onSubmit={this.handleSubmit}>
      <label>
        Name:
        <input
          defaultValue="Bob"
          type="text"
          ref={(input) => this.input = input} />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
}
```

checkbox和radio使用`defaultChecked`，select使用`defaultValue`。