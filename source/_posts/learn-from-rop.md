---
title: ROP 项目所得
date: 2017-03-17 22:28:59
tags: [总结]
---

最近几周一直在做 ROP 的权限模块，包括了前端和后端接口，前端使用了 `react`，UI 使用 `ant design`，后端使用 `nodejs` + `mongodb`，框架使用 `keystone`。好久没写 `react` 了，趁此机会也把 `react` 好好捡了捡，也发现了一些以前没有注意到的不合理的或者错误的用法。

因为是中途加入项目，也总结了一些问题，在此都做下记录。

PS. `ant design` 和 `keystone` 真是好用

<!-- more -->

## React

### 1.不在 JSX 的属性使用 bind()

而是在构造函数中使用 `bind()`

以前的用法是：

```jsx
render() {
  return (
    <Table
      onChange={this.handleTableChange.bind(this)}
    />
  );
}
```

正确的方式应该是在构造函数中绑定 `this`:

```jsx
constructor(...args) {
    super(...args);
    // 绑定 this
    this.handleTableChange = this.handleTableChange.bind(this);
}

handleTableChange() {
    //...
}

render() {
    return (
        <Table
            onChange={this.handleTableChange}
        />
    );
}
```

或者直接使用 `es7` 风格：

```jsx
handleTableChange = () => {
  //...
}

render() {
    return (
        <Table
            onChange={this.handleTableChange}
        />
    );
}
```

### 2.使用 props 传递数据流 / 不要滥用 ref

在使用 `react` 的过程中，经常会出现父组件要获取子组件内部状态的情况，在我之前使用 `react` 的过程中，我一直都是用的 `ref`，今天反思了一下，可能是在发现 `ref` 可以获取组件的 `DOM` 和实例之后觉得这个东西非常好用才开始滥用 `ref` 的。。

以前的使用方式：

```jsx
// 父组件
class Parent extends React.Component {

    handleClick = () => {
        const dog = this.refs.child.getDog();
        //...
    }

    render() {
        return (
            <div>
                <Child ref="child" />
                <Button onClick={this.handleClick} />
            </div>
        );
    }
}

// 子组件
class Child extends React.Component {

    state = {
        dog: 'aoliao',
    };

    getDog = () => {
        return this.state.dog;
    }

    render() {
        const { dog } = this.state;
        return (
            <div>
                {dog}
            </div>
        );
    }
}
```

我不知道有没有人和我一样这样用。。本来我还觉得是很正常的使用方法，其实因为过度使用 `ref`，使得整个设计都会有问题，因为可以通过 `ref` 拿子组件内部的数据，所以就让每个子组件自己维护自己的内容，好像独立了起来（听起来还挺有道理），但是长此下去，整个数据流就变得很混乱，也会出现过度封装的情况。

正确的方式是使用 `props` 来传递数据，既然 `dog` 这个属性在父组件要用到，那么就把 `dog` 放在父组件中，处理逻辑也就都移到父组件里去了，子组件只是通过 `prop` 拿着父组件的 `dog` 去干事情，但最终 `dog` 还是在父组件中，就好像角色倒过来了一样。

```jsx
// 父组件
class Parent extends React.Component {

    state = {
        dog: 'aoliao',
    }

    handleClick = () => {
        const { dog } = this.state;
        //...
    }

    render() {
        const { dog } = this.state;
        return (
            <div>
                <Child dog={dog} />
                <Button onClick={this.handleClick} />
            </div>
        );
    }
}

// 子组件
class Child extends React.Component {

    state = {
        dog: this.props.dog,
    }

    // 如果属性会变化，使用 componentWillReceiveProps 监听属性的变化
    componentWillReceiveProps = (nextProps) => {
        // 比较新老属性的不同
        if (this.props.dog != nextProps.dog) {
            this.setState({
                dog: nextProps.dog,
            });
        }
    }

    render() {
        const { dog } = this.state;
        return (
            <div>
                {dog}
            </div>
        );
    }
}
```

### 3.定义 propTypes 时使用 shape 代替 object

当组件需要接收属性时，定义 `propTypes` 检查是一个好的习惯。

另外根据 `eslint` 中的 `react/forbid-prop-types` 校验规则，应该避免使用 `object` 和 `array`，而使用更详细的 `shape` 和 `arrayOf()`

```jsx
// bad
UserDetailsPanel.propTypes = {
    form: React.PropTypes.object,
    checkedKeys: React.PropTypes.array,
};

// good!
UserDetailsPanel.propTypes = {
    form: React.PropTypes.shape({
        getFieldDecorator: React.PropTypes.func,
        getFieldsError: React.PropTypes.func,
        setFieldsValue: React.PropTypes.func,
        validateFields: React.PropTypes.func,
    }),
    checkedKeys: React.PropTypes.arrayOf(React.Proptypes.string),
};
```

### 4.ant design 里异步加载树数据时 Cannot set property 'checked' of undefined

这个问题找了好一会，起初是发现同时设置 `onCheck`和 `checkedKeys` 属性时会报错，后来发现是异步加载数据的问题。

其实 ant design 文档的最后也有说明，当树的数据是异步加载时，应该使用：

```jsx
{
    loading ? '...' : (
        <Tree>{this.state.treeData.map(data => <TreeNode />)}</Tree>
    )
}
```

### 5.无状态的组件使用纯函数(pure function)
```jsx
export default () => (
  <div>
    这个页面可不是一般人能看见的
  </div>
);
```

## 配置

### 1.webpackJsonp is not defined 错误

原因是使用了 `CommonChunkPlugin` 但没有首先引用公共文件

```jsx
// webpack.config.js
module.exports = {
    // 在这里使用了 CommonChunkPlugin 插件
    plugins: [
        new webpack.optimize.CommonsChunkPlugin({ name: 'commons' })
    ]
}

// index.jade
extends ../layouts/default

block content
    .container
        div#app
	// 必须要引入 commons.js
    script(src= baseUrl + 'commons.js')
```

### 2.regeneratorRuntime is not defined 错误

这个错误是我想在前端引入 react，并使用 es6 风格编码时发生的。

原因是没有引入 `babel-polyfill`

```jsx
// webpack.config.js
module.exports = {
    entry: {
        // '文件名': '引用的模块'
        'babel-polyfill': 'babel-polyfill',
        // ...
    }
    //...
}

// index.jade
extends ../layouts/default

block content
    .container
        div#app
	// 根据 config 文件中定义的文件名引入 babel-polyfill.js
    script(src= baseUrl + 'babel-polyfill.js')
```

### 3.eslint 开启 es7 风格

`babel-eslint` 默认支持 es7 风格的检查，在配置文件 `.eslintrc.js` 中添加一个属性即可。

```jsx
module.exports = {
    parser: "babel-eslint",
    // ...
}
```
