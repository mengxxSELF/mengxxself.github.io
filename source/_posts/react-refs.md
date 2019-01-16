---
title: react-refs
date: 2019-01-08 10:28:05
categories: react
tags: [react]
---

> refs

<!-- More -->

react 中通过设置ref可以获取react元素

* 直接使用ref


* createRef

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  values () {
    const val = this.myInput.current
  }
  render() {
    return <div>
      <div ref={this.myRef} />
      <input ref={this.myInput} />
    </div>
  }
}
```

[refs and the dom](https://reactjs.org/docs/refs-and-the-dom.html)