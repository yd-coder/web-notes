# React

参考文档：https://react.docschina.org/docs/getting-started.html

[TOC]



## 1. state与setState

### 正确地使用 State

关于 `setState()` 你应该了解三件事：

### 不要直接修改 State

例如，此代码不会重新渲染组件：

```react
// Wrong
this.state.comment = 'Hello';
```

而是应该使用 `setState()`:

```react
// Correct
this.setState({comment: 'Hello'});
```

构造函数是唯一可以给 `this.state` 赋值的地方。

### State 的更新可能是异步的

出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用。

因为 `this.props` 和 `this.state` 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。

例如，此代码可能会无法更新计数器：

```react
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

要解决这个问题，可以让 `setState()` 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数：

```react
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

上面使用了[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，不过使用普通的函数也同样可以：

```react
// Correct
this.setState(function(state, props) {
  return {
    counter: state.counter + props.increment
  };
});
```

### State 的更新会被合并

当你调用 `setState()` 的时候，React 会把你提供的对象合并到当前的 state。

例如，你的 state 包含几个独立的变量：

```react
  constructor(props) {
    super(props);
    this.state = {
      posts: [],      comments: []    };
  }
```

然后你可以分别调用 `setState()` 来单独地更新它们：

```react
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments      });
    });
  }
```

这里的合并是浅合并，所以 `this.setState({comments})` 完整保留了 `this.state.posts`， 但是完全替换了 `this.state.comments`。





## 2. 条件判断

在jsx中，我们在大括号{}内只能写表达式，不能编写逻辑代码，如if判断、for循环，此时我们可以通过短路逻辑判断，如：

下面例子写了当state的goods长度不为0时则展示对应数据

```react
export default class CartSample extends Component {
    this.state = {
      goods: [
      	{ id: 1, text: "web全栈架构师", count: 2 },
      ]
    }
    render() {
        return (
        	<ul>
              {this.state.goods.length && this.state.goods}
            </ul>
        )
    }
}
```

## 3. 循环展示列表

引用2的例子，循环展示商品列表，因为{}里面只能是表达式，因此我们的思路是遍历数组的每个元素，并返回不同的值，因此数组内置的map函数很适合做这种工作。

```react
export default class CartSample extends Component {
   state = {
      goods: [
      	{ id: 1, text: "web全栈架构师", count: 2 },
      	{ id: 2, text: "python全栈架构师", count: 0 },
      ]
    }
    render() {
        return (
        	<ul>
              {this.state.goods.length && this.state.goods.map( item => {
              	<li>{item.name}</li>
              })}
            </ul>
        )
    }
}
```

## 4. 两种组件写法

```react
// 方式1： 类
import React, { Component } from 'react'

export default class App extends Component {
  render() {
    return (
      <div>我是一个类组件</div>
    )
  }
}

// 方式2： 函数
import React from 'react'

export default function App() {
  return (
    <div>我是一个函数式组件</div>
  )
}
```

本来两者是有些差异的，hook出现之前，用函数式写组件一般都是只写傻瓜式组件，也就是没有处理业务逻辑的。但是自从react 16.80版本出现后，新增加了hook，它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性，因此可以试着去学学hook，这样子也可以在function里编写逻辑代码，也更简练。



## 5. 事件绑定

React 元素的事件处理和 DOM 元素的很相似，但是有一点语法上的不同：

- React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
- 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

```react

export default class CartSample extends Component {
    this.state = {
      text: ''
    }
    setGoodsText = e => {
        this.setState({
          text: e.target.value
        })
  	}
    render() {
        return (
        	<input type="text" onChange={this.setGoodsText}/>
        )
    }
}
```

上面是最简单的例子

那么传参事件该怎么绑定呢？

学之前先注意一点： {} 是个表达式

因为是表达式，如果我们要改上面代码为带参事件，我们不能用

```react
onChange={this.setGoodsText(args)}
```

这样子的话会被立刻执行，因此我们需要让他监听一个匿名函数，写法如下

```react
onChange={() => this.setGoodsText(args)}
```

在 React 中另一个不同点是你不能通过返回 `false` 的方式阻止默认行为。你必须显式地使用 `preventDefault`。例如，传统的 HTML 中阻止表单的默认提交行为，你可以这样写：

```html
<form onsubmit="console.log('You clicked submit.'); return false">
  <button type="submit">Submit</button>
</form>
```

在 React 中，可能是这样的：

```react
const Form = () => {
  const handleSubmit = (e) => {
    e.preventDefault(); // 取消默认行为  console.log('You clicked submit.');
  }

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Submit</button>
    </form>
  );
}
```





## 6.类方法中的this指向问题

给JSX绑定事件时由于没有指定上下文，`this指向会指向顶层`。

而指向顶层有两种情况：严格模式与非严格模式，而react是在`严格模式下`的，在<React.StrictMode>定义，所以this为undefined。

为了让我们方法中的this可以指向类本身，我们可以选择四种方式：

```react
// 方式一 构造器中给对应函数指定上下文， 如
this.addGood = this.addGood.bind(this)       ------>  推荐

// 方式二 在JSX给种绑定this
<div @click="{ this.addGood.bind(this) }"></div>

// 方式三 类里面方法用箭头函数定义，箭头函数是没有上下文的，上下文会指向类
addGood = () => {}                           ------>  推荐

// 方式四 JSX里用箭头函数
addGood = () => { () => {this.addGood()} }
```



## 7. setState注意事项

使用setState修改值有两个方法：

一个是传入对象修改，另一个方式是传入函数修改

```react
// 方式一
state = {
	text: ''
}
this.setState({
	text: '新值'
})

// 方式二
state = {
	text: ''
}
this.setState(prevState => {
	let text = '123'
	prevState.text = text
})
```

### 7.1 方式一注意事项

在使用方式一时要注意，若多次使用方式一进行setState，react最终会将多个setState操作给合并，这时候，若是多次setState了通过值，则只取最后一次setState的时候的值，如：

```
state = {
	text: ''
}
this.setState({
	text: '新值'
})
this.setState({
	text: '新新值'
})
```

最后的值为 ： 新新值

### 7.2 方式二注意事项

使用方式二时，我们最好按照react给我们定的规范，每次都给state需要修改的对象重新设置一个新的的值。

如：

```js
this.state = {
      goods: [
        { id: 1, text: "web全栈架构师" },
        { id: 2, text: "python全栈架构师" }
      ],
      text: ''
}
textChange = e => {
    this.setState({ text: e.target.value });
};
<input type="text" value={this.state.text} onChange={this.textChange} />
<button onClick={this.addGood}>添加商品</button>
```

现在当点击某个按钮时会触发addGood事件，addGood用来添加新商品数据到goods中，此时addGood的写法是这样的

```js
addGood = () => {
    this.setState(prevState => {
      return {
        goods: [
          ...prevState.goods,
          {
            id: prevState.goods.length + 1,
            text: prevState.text
          }
        ]
      };
    });
  }
```

会将以前state.goods里的数据展开，拷贝进一个新的数组，再将新数据加进去，这就是react的规范。

## 8. antd框架引入

安装：

```
npm install antd --save
```

试用按钮组件：

```js
import Button from 'antd/lib/button';
import 'antd/dist/antd.css'

export default class App extends Component {
  render() {
    return (
      <div>
        <Button type='primary'>Button</Button>
      </div>
    )
  }
}
```

上面那种方式引入很麻烦，而且是样式是引用全部的样式表，如何按需引入呢？

**安装react-app-rewired取代react-scripts，可以扩展webpack的配置，类似vue.config.js**

```js
npm install react-app-rewired@2.0.2-next.0 babel-plugin-import --save
```

修改package.json的scripts字段：

```js
// 所有react-scripts 都替换成 react-app-rewired 如
"scripts": {
    "start": "react-app-rewired start",
	...
},
```

在根目录下新建config-overrides.js，内容如下

```js
const { injectBabelPlugin } = require("react-app-rewired");
module.exports = function override(config, env) {
  config = injectBabelPlugin(
    // 在默认配置基础上注入
    // 插件名，插件配置
    ["import", { libraryName: "antd", libraryDirectory: "es", style: "css" }],
    config
  );
  return config;
};

```

这样子配置就完成了，接下来我们可以实现按需导入了，在需要引入组件的地方：

```js
import {Button} from 'antd'

<Button type='primary'>Button</Button>
```

## 9. React.lazy

`React.lazy` 函数能让你像渲染常规组件一样处理动态引入（的组件）。

**使用之前：**

```react
import OtherComponent from './OtherComponent';
```

**使用之后：**

```react
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

此代码将会在组件首次渲染时，自动导入包含 `OtherComponent` 组件的包。

`React.lazy` 接受一个函数，这个函数需要动态调用 `import()`。它必须返回一个 `Promise`，该 Promise 需要 resolve 一个 `default` export 的 React 组件。

然后应在 `Suspense` 组件中渲染 lazy 组件，如此使得我们可以使用在等待加载 lazy 组件时做优雅降级（如 loading 指示器等）。

```react
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

`fallback` 属性接受任何在组件加载过程中你想展示的 React 元素。你可以将 `Suspense` 组件置于懒加载组件之上的任何位置。你甚至可以用一个 `Suspense` 组件包裹多个懒加载组件。

## 10. 性能优化：浅比较

参考文档：https://react.docschina.org/docs/optimizing-performance.html

UI 更新需要昂贵的 DOM 操作，而 React 内部使用几种巧妙的技术以便最小化 DOM 操作次数。对于大部分应用而言，使用 React 时无需专门优化就已拥有高性能的用户界面。尽管如此，你仍然有办法来加速你的 React 应用。

还是引用9的例子，只不过我们会给CommentList加一个定时器，每隔1.5s就对comments列表进行setState操作，但是我们不修改它的值：

```react
componentDidMount() {
    this.timmer = setInterval(() => {
      this.setState({
        comments: [
          { name: 'cai', age: 20, education: 'university' },
          { name: 'chen', age: 22, education: 'university' },
        ]
      })
    }, 1500)
}
```

然后再在Comment组件打印日志。我们会发现打印日志会每隔1.5s打印，这时候问题就来了，明明我们的数据没发生实质上的变化，但页面却一直在渲染，这是一个非常值得优化的点。

解决方案：

### 10.1 使用shouldComponentUpdate

因为使用到生命周期函数，所以我们的Comment组件应该转换为类形式，shouldComponentUpdate用来检查旧值与新值是否相等，返回一个bool值，如为true则重新渲染，否则不渲染

```react
class Comment extends React.Component{

  constructor(props) {
    super(props)
  }

  shouldComponentUpdate(nextProps){
      if (nextProps.data.name === this.props.data.name &&
        nextProps.data.age === this.props.data.age &&
        nextProps.data.education === this.props.data.education
        ) {
          return false;
      }
      return true;
  }

  render() {
    console.log("render comment");

    return (
      <div>
          <p>{this.props.data.name}</p>
          <p>---   {this.props.data.age}</p>
          <p>---   {this.props.data.education}</p>
      </div>
    );
  }
}
```

### 10.2 使用PureComponent

首先我们先研究一下PureComponent的实现原理：

PureComponent首先会判断新值与旧值**是否在同一片内存地址**，如果是，则返回true，也就是相同。

否则会再对旧值的内部做一层循环比较（注意：浅比较只会进行一层for循环，有**深层次的结构时浅比较是没有效果的**）

由此可以看出PureComponent的两个缺点：

一、如果是一个对象，或者数组，我们对其内部进行修改，而**没有改变内存地址**时，页面不会重新渲染

二、如果包含**二层及以上的层次结**构时，浅比较无法生效

如果我们有二层的层次结构时还想进行钱比较该怎么办呢？

方法： 传给展示组件的值用第二层的值传递，而非将整个对象或数组传递给展示组件。

继续引用9的例子



此时我们传给Comment组件的值不传入每个comments列表项，而是用展开运算符将每个对象展开，传入最里边的值，意思就是，我有下面这个结构的数组：

```js
// 需要传给展示组件的数据
comments: [
          { name: 'cai', age: 20, education: 'university' },
          { name: 'chen', age: 22, education: 'university' },
 ]

// 传入的值：{...c}
{this.state.comments.map((c, i) => {
          return <Comment key={i} {...c}></Comment>
})}

// 此时...c == 每个对象的name、age、education值

// 展示组件继承PureComponent
class Comment extends React.PureComponent{
    constructor(props) {
        super(props)
    }
    render() {
    console.log("render comment");

    return (
      <div>
          <p>{this.props.name}</p>
          <p>---   {this.props.age}</p>
          <p>---   {this.props.education}</p>
      </div>
    );
}
```

### 10.3 使用memo

类形式的组件可以进行浅比较，现在在React v16.6.0之后，函数式也添加了浅比较，也就是memo， 使用方式如下：

```react
constJoke=React.memo(() => (
  <div>
    {this.props.value||'loading...'}
  </div>
));
```

只需将组件用React.memo包裹即可。

## 11. 高阶组件

### 11.1 核心概念

**高阶组件是参数为组件，返回值为新组件的函数**

高阶组件其实就是装饰器的原型，装饰器内部就是对高阶组件封装了而已，也就是语法糖。

高阶组件的一个最明显的好处就是你可以自行扩展组件行为。想到行为扩展想必你已经联想到mixins了，那为什么不用mixins而非要弄个高阶组件出来呢？ 参考文档：https://react.docschina.org/blog/2016/07/13/mixins-considered-harmful.html

接下来我们就讲讲高阶组件的使用：

假设有一个 `CommentList` 组件，它订阅外部数据源，用以渲染评论列表：

```react
class CommentList extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      // 假设 "DataSource" 是个全局范围内的数据源变量
      comments: DataSource.getComments()
    };
  }

  componentDidMount() {
    // 订阅更改
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    // 清除订阅
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    // 当数据源更新时，更新组件状态
    this.setState({
      comments: DataSource.getComments()
    });
  }

  render() {
    return (
      <div>
        {this.state.comments.map((comment) => (
          <Comment comment={comment} key={comment.id} />
        ))}
      </div>
    );
  }
}
```

稍后，编写了一个用于订阅单个博客帖子的组件，该帖子遵循类似的模式：

```react
class BlogPost extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      blogPost: DataSource.getBlogPost(props.id)
    };
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    this.setState({
      blogPost: DataSource.getBlogPost(this.props.id)
    });
  }

  render() {
    return <TextBlock text={this.state.blogPost} />;
  }
}
```

`CommentList` 和 `BlogPost` 不同 - 它们在 `DataSource` 上调用不同的方法，且渲染不同的结果。但它们的大部分实现都是一样的：

- 在挂载时，向 `DataSource` 添加一个更改侦听器。
- 在侦听器内部，当数据源发生变化时，调用 `setState`。
- 在卸载时，删除侦听器。

你可以想象，在一个大型应用程序中，这种订阅 `DataSource` 和调用 `setState` 的模式将一次又一次地发生。我们需要一个抽象，允许我们在一个地方定义这个逻辑，并在许多组件之间共享它。这正是高阶组件擅长的地方。

对于订阅了 `DataSource` 的组件，比如 `CommentList` 和 `BlogPost`，我们可以编写一个创建组件函数。该函数将接受一个子组件作为它的其中一个参数，该子组件将订阅数据作为 prop。让我们调用函数 `withSubscription`：

```react
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
```

第一个参数是被包装组件。第二个参数通过 `DataSource` 和当前的 props 返回我们需要的数据。

当渲染 `CommentListWithSubscription` 和 `BlogPostWithSubscription` 时， `CommentList` 和 `BlogPost` 将传递一个 `data` prop，其中包含从 `DataSource` 检索到的最新数据：

```react
// 此函数接收一个组件...
function withSubscription(WrappedComponent, selectData) {
  // ...并返回另一个组件...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ...负责订阅相关的操作...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

请注意，HOC 不会修改传入的组件，也不会使用继承来复制其行为。相反，HOC 通过将组件*包装*在容器组件中来*组成*新组件。HOC 是纯函数，没有副作用。

被包装组件接收来自容器组件的所有 prop，同时也接收一个新的用于 render 的 `data` prop。HOC 不需要关心数据的使用方式或原因，而被包装组件也不需要关心数据是怎么来的。

因为 `withSubscription` 是一个普通函数，你可以根据需要对参数进行增添或者删除。例如，您可能希望使 `data` prop 的名称可配置，以进一步将 HOC 与包装组件隔离开来。或者你可以接受一个配置 `shouldComponentUpdate` 的参数，或者一个配置数据源的参数。因为 HOC 可以控制组件的定义方式，这一切都变得有可能。

与组件一样，`withSubscription` 和包装组件之间的契约完全基于之间传递的 props。这种依赖方式使得替换 HOC 变得容易，只要它们为包装的组件提供相同的 prop 即可。例如你需要改用其他库来获取数据的时候，这一点就很有用。

### 11.2 **高阶组件的链式调用**

比如我们有两个高阶组件，分别定义不同的行为：

```react
// 高阶组件1
const withLog = Comp => {
  console.log("日志打印...");
  return props => <Comp {...props} />;
};
// 高阶组件2
const withSubscribtion = Comp => {
  // 获取name,name可能来自于接口或其他手段
  const name = "其他属性";
  console.log("do something");
  return class extends React.Component {
    render() {
      return <Comp {...this.props} name={name} />;
    }
  };
};
// 被扩展组件
class NeedToExtend extends Component {
  render() {
    return (
      <div></div>
    );
  }
}
// 链式调用过程
const newComp = withLog(withSubscribtion(NeedToExtend))
```

### 11.3 装饰器语法糖使用

ES7装饰器可用于简化高阶组件写法

```
npm i -D babel-plugin-transform-decorators-legacy
```

在config-overrides.js添加配置

```js
config = injectBabelPlugin(
    ["@babel/plugin-proposal-decorators", { legacy: true }],
    config
);
```

使用装饰器，以11.2为例子

```react
// 链式调用过程
const newComp = withLog(withSubscribtion(NeedToExtend))

// 装饰器使用
@withSubscribtion
@withLog
class NeedToExtend extends Component {
  render() {
    return (
      <div></div>
    );
  }
}
```

## 12. 复合组件

### 12.1 复合内容为jsx

复合组件其实就是展示组件预留个插口位置待容器组件插入相应的值，等同于Vue中的插槽。

对应Vue中的匿名插槽就是`props.children`，如:

```react
import React, { Component } from 'react';

// 展示组件
function Dialog(props) {
  return (
    <div>
      {props.children}
    </div>
  );
}

// WelcomeDialog通过复合提供内容
function WelcomeDialog(props) {
  return (
    <Dialog {...props}>
      <h1>欢迎光临</h1>
      <p>感谢使用react</p>
    </Dialog>
  );
}

export default function() {
  return (
    <div>
      <WelcomeDialog></WelcomeDialog>
    </div>
  )
}
```

此时`{props.children}`就是插进来的内容

```html
<h1>欢迎光临</h1>
<p>感谢使用react</p>
```

那么对应Vue的有名插槽是怎么实现的呢？ 

其实很简单，就是通过props传值，如我在展示组件里在定义一个页脚，页脚内容通过容器组件传入：

```react
function Dialog(props) {
  return (
    <div>
      {props.children}
      <div className="footer">{props.footer}</div>
    </div>
  );
}
const footer = <button onClick={() => alert("确定！")}>确定</button>;
<WelcomeDialog color="green" footer={footer} />
```

以上例子复合内容形式均为jsx。

### 12.2 复合内容为函数

复合内容为函数时就相当于Vue中的作用于插槽，例子如下

假设我现在定义一个Fetcher组件，组件内的复合内容是一个带参函数，我想通过这个函数的参数渲染对应的内容：

```react
<Fetcher name="getUser">
	{user => (
		<p>
			{user.name}-{user.age}
		</p>
	)}
</Fetcher>
```

定义组件：

```js
// api接口
const Api = {
  getUser() {
    return { name: "jerry", age: 20 };
  }
};
// 组件
function Fetcher(props) {
  const user = Api[props.name](); //获取name并执行函数获取数据
  return props.children(user);  //通过props.children返回数据
}
```

### 12.3 复合内容为数组

复合内容为数组时其实实质上也是jsx

假如我们想要对复合内容（`类型为数组的jsx`）进行过滤，值过滤出类型为type的元素进行展示：

我们可以用到`React.Children.map`方法，遍历复合内容，并过滤出我们想要的

```react
function Filter({ children, type }) {
  return (
    <div>
      {React.Children.map(children, child => {
        if (child.type !== type) {
          return;
        }
        return child;
      })}
    </div>
  );
}
<Filter type="p">
    <h1>react</h1>
    <p>react很不错</p>
    <h1>vue</h1>
    <p>vue很不错</p>
</Filter>
```

若我们想对传进来的复合内容进行修改，则必须谨记：

**复合内容为jsx，是虚拟节点，内容不能修改**，因此如果我们要修改它的内容，那我们只能克隆对应的元素在做自己想要的修改即可，如：

现在我有一个按钮组，结构如下

```html
<RadioGroup name="mvvm">
    <Radio value="vue">vue</Radio>
    <Radio value="react">react</Radio>
    <Radio value="react">angular</Radio>
</RadioGroup>
```

我不想给每个按钮都加一个`name`，而是给`Radio`的父组件`RadioGroup`加一个`name`，然后让`RadioGroup`循环遍历每个`Radio`，对每个`Radio`加上同一个`name`该怎么操作呢？

如下

```react
function RadioGroup(props) {
  return (
    <div>
      {React.Children.map(props.children, child => {
        //   vdom不可更改，克隆一个新的去改才行
        return React.cloneElement(child, { name: props.name });
      })}
    </div>
  );
}

function Radio({children, ...rest}) {
  return (
    <label>
      <input type="radio" {...rest} />
      {children}
    </label>
  );
}
```

这里需要注意的一点就是，再给`Radio`赋值的时候要注意`props`包含`children`和`父级组件传进来的值`。

因此Radio接收参数时需要做分割，分为`children复合内容部分`和`剩余部分`，`剩余部分`传给input作为属性值。



## 13 DOM对象和useRef()

Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。

在典型的 React 数据流中，[props](https://zh-hans.reactjs.org/docs/components-and-props.html) 是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型数据流之外强制修改子组件。被修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。对于这两种情况，React 都提供了解决办法。

### 


## 13. Hook

*Hook* 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性

你之前可能把它们叫做“无状态组件”。但现在我们为它们引入了使用 React state 的能力，所以我们更喜欢叫它”函数组件”。

1. useState
2. useEffect
3. useContext
4. useReducer
5. useCallback
6. useRef
7. useMemo
8. useImperativeHandle
9. useLayoutEffect
10. useDebugValue（18.0新增）
11. useDeferredValue（18.0新增）
12. useTransition（18.0新增）
13. useId（18.0新增）
14. useSyncExternalStore（18.0新增）
15. useInsertionEffect（18.0新增）

### 自定义Hook(补充)

自定义hook可以让我们将组件逻辑提取到一个可重复利用的组件内。

这里会有两个案例讲解自定义hook的使用

**第一个简单的例子**：

```react
import {useState, useEffect} from 'react'

function Comp() {
  let [num, setNum] = useState(0)

  useEffect(() => {
    setTimeout(() => {
      setNum(++num)
    }, 2000)
  }, [])

  return (
    <h2>Comp -- {num}</h2>
  )
}

function App() {
  return (
    <div className="App">
        <Comp1></Comp1>
    </div>
  );
}

export default App;
```

如上所示，我们可以将`Comp`组件的

```react
let [num, setNum] = useState(0)

useEffect(() => {
  setTimeout(() => {
    setNum(++num)
  }, 2000)
}, [])
```

给提取到一个单独的组件中，这时候便是一个自定义hook，使得我们调用的代码更简洁，且逻辑抽离到了一起。

我们将上述代码抽离到`./hooks/numHook.js`中。

```react
import {useState, useEffect} from 'react'

export default function useNum() {
  let [num, setNum] = useState(0)

  useEffect(() => {
    setTimeout(() => {
      setNum(++num)
    }, 2000)
  }, [])
  return [num, setNum]
}
```

这时候我们在需要引用该hook的地方进行导入引用即可，例子将可以改写为：

```react
import useNum from './hooks/numHook'

function Comp() {
  
  let [num] = useNum()
  return (
  <h2>Comp -- {num}</h2>
  )
}

function App() {
  return (
    <div className="App">
        <Comp></Comp>
    </div>
  );
}

export default App;
```

这就是最简单的自定义hook。

### 自定义请求数据hook

这里我们要做的自定义Hook是发起数据请求的hook

实现效果如下：

![image-20210530111434733](https://gitee.com/cai-lunduo/react-selfdefine-hook/raw/master/public/effects.jpg)

安装bootstrap:

```shell
npm i -S bootstrap
```

**server端**：

```js
const express = require('express');
const cors = require('cors');

const app = express();
app.use(cors())

app.get('/list', (req, res) => {

  // 当前页和每页多少条
  let {currentPage, perSize} = req.query
  perSize = parseInt(perSize, 10)
  // 数据 页数和条数对应得数据
  let list = []

  // 总数据条数
  let total = 66

  // 总页数
  let pageCount = Math.ceil(total / perSize)

  // 起始索引
  let offset = (currentPage - 1) * perSize

  if(currentPage >= pageCount) {
    perSize = total % perSize
  }

  for(let i = offset; i < offset + perSize; i++) {
    list.push({id: i+1, name: 'cai-'+(i+1)})
  }

  res.json({
    currentPage,
    perSize,
    total,
    pageCount,
    list
  })
})

app.listen(8080, () => {
  console.log('listening on 8080...')
})
```

记得先启动服务端：

```node
node server
```

在`./hooks/useRequest.js`中：

```react
import {useState, useEffect} from 'react'

export default function useRequest() {
  /* 用户可更改的请求数据 */
  let [options, setOptions] = useState({currentPage: 1, pageSize: 10})
  let [data, setData] = useState({
    total: 0,
    pageCount: 0,
    list: []
  })

  // 请求数据
  function reqeust() {
    let {currentPage, pageSize} = options
    fetch(`http://localhost:8080/list?currentPage=${currentPage}&perSize=${pageSize}`)
      .then(res => res.json())
      .then(res => setData({...res}))
  }

  useEffect(reqeust, [options])

  return [data, options, setOptions]
}
```

这里的options state是单独抽离出来的，因为我们要返回给组件setOptions让他自定义请求参数（如：用户点击下一页时我们要重新改变请求参数的`currentPage`为当前页+1）。

```js
onClick={() => {
	setOptions({ ...options, currentPage: index + 1 })
}}
```

组件一旦挂载我们就发起数据请求，然后将通过`setData`设置数据，并将`data`返回。可以看到最后返回的格式是[data, options, setOptions]，意味着我们在组件内可以通过类似`useState`的方式获取自定义hook的值。

下面组件的取法：

```js
 let [data, options, setOptions] = useRequest()
```

在`./pages/Tabel.js`中：

```react
import {useState} from 'react'
import useRequest from '../hooks/useRequest'

export default function Table() {
  let [data, options, setOptions] = useRequest()

  let { list, pageCount } = data

  let [size, setSize] = useState(10)

  return (
    <>
      <table className="table table-striped">
        <thead>
          <tr>
            <th>id</th>
            <th>name</th>
          </tr>
        </thead>
        <tbody>
          {list.map((item) => {
            return (
              <tr key={item.id}>
                <td>{item.id}</td>
                <td>{item.name}</td>
              </tr>
            )
          })}
        </tbody>
      </table>
      <div>
        <nav>
          {/* 分页组件 */}
          <ul className="pagination">
            {new Array(pageCount).fill(0).map((item, index) => {
              return (
                <li
                  className="page-item"
                  key={index}
                  onClick={() => {
                    setOptions({ ...options, currentPage: index + 1 })
                  }}
                >
                  <a className="page-link" href="#">
                    {index + 1}
                  </a>
                </li>
              )
            })}
          </ul>
        </nav>

        {/* 下拉框 */}
        <select 
          className="form-select"
          value={size}
          onChange={(e) => {setSize(e.target.value); setOptions({currentPage: 1, pageSize: e.target.value})}}
        >
          <option value={5}>5</option>
          <option value={10}>10</option>
          <option value={20}>20</option>
        </select>
      </div>
    </>
  )
}
```



### 13.1 useState

useState的用法很简单也很方便，比起class的方式简直要简洁得不少

```react
import React, { useState } from 'react';

export default function Example() {
  const [count, setCount] = useState(0)
  const [fruit, setFruit] = useState('banana')
  return (
    <div>
      你点击了{count}次
      <button onClick={() => setCount(count+1)}>点击</button>
      <p>{fruit}</p>
    </div>
  )
}
```

如上例子所示，useState(0)，0表示初始值，而将useState解构成两个成员，一个是状态属性，一个是更改状态的函数，其中useState有多个，这样子就比class的形式要简洁多了。

### 13.2 useEffect

你之前可能已经在 React 组件中执行过数据获取、订阅或者手动修改过 DOM。我们统一把这些操作称为“副作用”，或者简称为“作用”。

`useEffect` 就是一个 Effect Hook，给函数组件增加了操作副作用的能力。它跟 class 组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 具有相同的用途，只不过被合并成了一个 API。

例如，下面这个组件在 React 更新 DOM 后会设置一个页面标题：

```react
import React, { useState, useEffect } from 'react';

export default function Example() {
  const [count, setCount] = useState(0)
  const [fruit, setFruit] = useState('banana')
  //相当于componentDidMount 和 componentDidUpdate
  useEffect(() => {
    // 使用浏览器的API更新页面标题
    document.title = `您点击了 ${count} 次`
  })

  return (
    <div>
      你点击了{count}次
      <button onClick={() => setCount(count+1)}>点击</button>
    </div>
  )
}
```

当你调用 `useEffect` 时，就是在告诉 React 在完成对 DOM 的更改后运行你的“副作用”函数。由于副作用函数是在组件内声明的，所以它们可以访问到组件的 props 和 state。默认情况下，React 会在每次渲染后调用副作用函数 —— **包括**第一次渲染的时候

副作用函数还可以通过返回一个函数来指定如何“清除”副作用。例如，在下面的组件中使用副作用函数来订阅好友的在线状态，并通过取消订阅来进行清除操作

```react
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);    return () => {      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);    };  });
  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

#### useEffect模拟重新发送请求

因为useEffect会在组件更新时调用，所以我们可以维护一个状态，当这个状态改变，我们调用函数重新发送请求。

```react
import React, { useState, useEffect } from 'react';

export default function Example() {
  const [count, setCount] = useState(0)
  const [flag, setFlag] = useState(false)
    
  function request() {
      setCount(++count)
  }
    
  useEffect(() => {
      request()
  },[flag])
  

  return (
    <div>
      你点击了{count}次
      <button onClick={() => setFlag(!flag)}>点击</button>
    </div>
  )
}
```



#### 13.2.1 useEffect的优化点

当然，`useEffect`和`useState`一样可以同时存在多个，还可以指定每次副作用函数只对那些起作用，和指定那些副作用函数只执行一次，因为比如我们要在`useEffect`中异步获取数据，我们总不能每次渲染操作都获取一次，因此有下面的解决方案：

```react
import React, { useState, useEffect } from 'react';

export default function Example() {
  const [count, setCount] = useState(0)
  const [fruit, setFruit] = useState('banana')
  //相当于componentDidMount 和 componentDidUpdate
  useEffect(() => {
    // 使用浏览器的API更新页面标题
    // 该副作用函数只对count和fruit起作用
    document.title = `您点击了 ${count} 次`
    console.log(count)
  }, [fruit, count])
  
  // 只会调用一次
  useEffect(() => {
    console.log('异步获取数据操作')
  },[])

  return (
    <div>
      你点击了{count}次
      <button onClick={() => setCount(count+1)}>点击</button>
    </div>
  )
}
```

如上所示，我们可以在useEffect传入一个数组参数，里面为空则表示只执行一次，如果里面有状态则表示只对里面的状态起作用。

![image-20210514205707734](https://gitee.com/cai-lunduo/react-markdown-notes/raw/master/public/clearEffect.png)

**为什么要在 effect 中返回一个函数？** 这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。

**React 何时清除 effect？** React 会在组件卸载的时候执行清除操作，相当于类组件的`componentWillUnmount`，也会在每次组件更新之前调用该回调，相当于`componentWillUpdate`。正如之前学到的，effect 在每次渲染的时候都会执行。这就是为什么 React *会*在执行当前 effect 之前对上一个 effect 进行清除。

#### 13.2.2 小结

了解了 `useEffect` 可以在组件渲染后实现各种不同的副作用。有些副作用可能需要清除，所以需要返回一个函数：

```react
 useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

其他的 effect 可能不必清除，所以不需要返回。

```react
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
```

#### 13.2.3 useEffect,useInsertionEffect,useLayoutEffect区别

组件挂载->state改变->DOM改变->绘制屏幕->useEffect

组件挂载->state改变->useInsertionEffect->DOM改变->绘制屏幕

组件挂载->state改变->DOM改变->useLayoutEffect->绘制屏幕





### useContext（补充）

我们通过`createContext`创建一个Context之后，在子组件可以通过`useContext`拿到我们在祖先组件创建的`Context`，但是前提是我们需要对子组件用`Context.Provider`标签进行包裹，然后通过`value`属性传值。

这里需要注意` const states = useContext(Context)`只能在子组件内部调用，不可在顶级元素使用。

例子如下：

```react
import {createContext, useContext} from 'react'

const Context = createContext()

function Child() {
  const states = useContext(Context)
  return (
    <div>
      {states.map(state => {
        return (
        <h2>{state}</h2>
        )
      })}
    </div>
  )
}

function App() {
  return (
    <div className="App">
      <Context.Provider value={[1,2,3]}>
        <Child></Child>
      </Context.Provider>
    </div>
  );
}

export default App;
```

在这里我们通过`Context.Provider`传输了一个数字数组到子代组件，子代组件通过`const states = useContext(Context)`获取到父组件传过来的数据并进行展示。



### UseMemo

useMemo和useCallback十分相似，useCallback用来缓存函数对象，useMemo用来缓存函数的执行结果。在组件中，会有一些函数具有十分的复杂的逻辑，执行速度比较慢。闭了避免这些执行速度慢的函数返回执行，可以通过useMemo来缓存它们的执行结果，像是这样：

```react
const result = useMemo(()=>{
    return 复杂逻辑函数();
},[依赖项])
```

useMemo中的函数会在依赖项发生变化时执行，注意！是执行，这点和useCallback不同，useCallback是创建。执行后返回执行结果，如果依赖项不发生变化，则一直会返回上次的结果，不会再执行函数。这样一来就避免复杂逻辑的重复执行。



### UseImperativeHandle

在React中可以通过forwardRef来指定要暴露给外部组件的ref：

```react
const MyButton = forwardRef((props, ref) => {
    return <button ref={ref}>自定义按钮</button>
});
```

上例中，MyButton组件将button的ref作为组件的ref向外部暴露，其他组件在使用MyButton时，就可以通过ref属性访问：

```react
<MyButton ref={btnRef}/>
```

通过useImperativeHandle可以手动的指定ref要暴露的对象，比如可以修改MyButton组件如下：

```react
const MyButton = forwardRef((props, ref) => {

    useImperativeHandle(ref,()=> {
        return {
            name:'孙悟空'
        };
    });

    return <button>自定义按钮</button>
});
```

useImperativeHandle的第二个参数是一个函数，函数的返回值会自动赋值给ref（current属性）。上例中，我们将返回值为`{name:'孙悟空'}`，当然返回孙悟空没有什么意义。实际开发中，我们可以将一些操作方法定义到对象中，这样可以有效的减少组件对DOM对象的直接操作。

```react
const MyButton = forwardRef((props, ref) => {

    const btnRef = useRef();

    useImperativeHandle(ref,()=> {
        return {
            setDisabled(){
                btnRef.current.disabled = true;
            }
        };
    });

    return <button ref={btnRef}>自定义按钮</button>
});

const App = () => {
    
    const btnRef = useRef();

    const clickHandler = () => {
        btnRef.current.setDisabled();
    };

    return <div>
        <MyButton ref={btnRef}/>
        <button onClick={clickHandler}>点击</button>
    </div>;
};
```



### UseDeferredValue

useDeferredValue用来设置一个延迟的state，比如我们创建一个state，并使用useDeferredValue获取延迟值：

```react
const [queryStr, setQueryStr] = useState('');
const deferredQueryStr = useDeferredValue(queryStr);
```

上边的代码中queryStr就是一个常规的state，deferredQueryStr就是queryStr的延迟值。设置延迟值后每次调用setState后都会触发两次组件的重新渲染。第一次时，deferredQueryStr的值是queryStr修改前的值，第二次才是修改后的值。换句话，延迟值相较于state来说总会慢一步更新。

延迟值可以用在这样一个场景，一个state需要在多个组件中使用。一个组件的渲染比较快，而另一个组件的渲染比较慢。这样我们可以为该state创建一个延迟值，渲染快的组件使用正常的state优先显示。渲染慢的组件使用延迟值，慢一步渲染。当然必须结合React.memo或useMemo才能真正的发挥出它的作用。



### UseTransition

当我们在组件中修改state时，会遇到复杂一些的state，当修改这些state时，甚至会阻塞到整个应用的运行，为了降低这种state的影响，React为我们提供了useTransition，通过useTransition可以降低setState的优先级。

useTransition会返回一个数组，数组中有两个元素，第一个元素是isPending，它是一个变量用来记录transition是否在执行中。第二个元素是startTransition，它是一个函数，可以将setState在其回调函数中调用，这样setState方法会被标记为transition并不会立即执行，而是在其他优先级更高的方法执行完毕，才会执行。

除了useTransition外，React还直接为为我们提供了一个startTransition函数，在不需要使用isPending时，可以直接使用startTransition也可以达到相同的效果。



### 优化

假如我们要给多个子组件提供同一份数据，那么我可以将提供数据的父组件给抽离成一个单独的负责**提供数据**的组件：

```react
import {createContext, useState} from 'react'

export const Context = createContext()

export function CountContextProvider({children}) {
  let [count, setCount] = useState(10)

  const countObj = {
    count,
    add() {
      setCount(++count)
    },
    minus() {
      setCount(--count)
    }
  }

  return (
    <Context.Provider value={countObj}>
      {children}
    </Context.Provider>
  )
}
```

子组件：

```react
import {useContext} from 'react'
import {Context, CountContextProvider} from './CountContextProvider'

function CountChild() {
  const {count, add, minus} = useContext(Context)
  return (
    <div>
      <h2>{count}</h2>
      <button onClick={() => {add()}}>增加</button>
      <button onClick={() => {minus()}}>减少</button>
    </div>
  )
}


let r = () => {
  return (
    <CountContextProvider>
      <CountChild/>
    </CountContextProvider>
  )
}

export default r
```



## 14. Context

Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。

因为典型的React应用数据是通过Props属性自上而下（由父及子）进行传递的，但这种做法对于某些类型的属性而言是极其繁琐的（例如：地区偏好，UI 主题），这些属性是应用程序中许多组件都需要的。Context 提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props。

总体步骤

```shell
1.creareContext 

2.MyContext.Provider包裹通过value传值 

3.在需要该值的组件static contextType = ThemeContext;接收context
```

### 14.1 使用Context之前

**何时使用 Context**

Context的目的是共享那些应该在全局共享的数据，例如当前用户的Token数据，或者首选语言。举个例子，在下面的代码中，我们通过一个 “theme” 属性手动调整一个按钮组件的样式：

```react
class App extends React.Component {
  render() {
    return <Toolbar theme="dark" />;
  }
}

function Toolbar(props) {
  // Toolbar 组件接受一个额外的“theme”属性，然后传递给 ThemedButton 组件。
  // 如果应用中每一个单独的按钮都需要知道 theme 的值，这会是件很麻烦的事，
  // 因为必须将这个值层层传递所有组件。
  return (
    <div>
      <ThemedButton theme={props.theme} />
    </div>
  );
}

class ThemedButton extends React.Component {
  render() {
    return <Button theme={this.props.theme} />;
  }
}
```

使用 context, 我们可以避免通过中间元素传递 props：

```react
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light');
class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。    
    // 无论多深，任何组件都能读取这个值。    
      // 在这个例子中，我们将 “dark” 作为当前的值传递下去。    
      return (
      <ThemeContext.Provider value="dark">        
              <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar() {  
    return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 读取当前的 theme context。  
  // React 会往上找到最近的 theme Provider，然后使用它的值。  
  // 在这个例子中，当前的 theme 值为 “dark”。  
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;  
  }
}
```

**使用Context之前应该有什么考虑？**

使用Context之前我们必须清楚，Context会使得组件的复用性变差，它主要应用场景在于*很多*不同层级的组件需要访问同样一些的数据，请谨慎使用。

如果只是为了避免层层传递一些属性，`组件组合`有时候是比Context更好的解决方案：

如：

```react
<Page user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<PageLayout user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<NavigationBar user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<Link href={user.permalink}>
  <Avatar user={user} size={avatarSize} />
</Link>
```

当我们发现user只在Avatar使用到但其他上层组件都要层层传递的时候，代码会变得非常冗余。解决方案除了用Context之外，我们还可以将该Avatar组件自身传递下去，这样中间组件无需知道 `user` 或者 `avatarSize` 等 props：



```react
function Page(props) {
  const user = props.user;
  const userLink = (
    <Link href={user.permalink}>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
  return <PageLayout userLink={userLink} />;
}

// 现在，我们有这样的组件：
<Page user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<PageLayout userLink={...} />
// ... 渲染出 ...
<NavigationBar userLink={...} />
// ... 渲染出 ...
{props.userLink}
```

这种对组件的*控制反转*减少了在你的应用中要传递的 props 数量，这在很多场景下会使得你的代码更加干净，使你对根组件有更多的把控。但是，这并不适用于每一个场景：这种将逻辑提升到组件树的更高层次来处理，会使得这些高层组件变得更复杂，并且会强行将低层组件适应这样的形式，这可能不会是你想要的。

而且你的组件并不限制于接收单个子组件。你可能会传递多个子组件，甚至会为这些子组件（children）封装多个单独的“接口（slots）

### 14.2 Context用法

Context可以有三种用法

#### 14.2.1 Consumer(函数组件)

就是通过Consumer包裹需要拿到值的元素，这样子那个元素就能拿到上下文的值了

```react
import React from 'react'

function Child(props) {
  return (
  <div>Child: {props.foo}</div>
  )
}

const MyContext = React.createContext()
const { Provider, Consumer } = MyContext;

export default function ContextTest() {
  return (
    <div>
      <Provider value={{foo: 'bar'}}>
        <Consumer>
          { value => <Child {...value}></Child> }
        </Consumer>
      </Provider>
    </div>
  )
}

```

Provider设置值value，然后Consumer就可以拿到值，Consumer内部组件可以通过Props.children格式为带参函数的形式拿到value，然后让对应组件渲染结果。

#### 14.2.2 Hook取值

Hook是一种很方便的存在，它让我们不用通过Consumer就可以很简便的取到上级传给我们的值，但是有版本限制，必须

```react
import React, {useContext} from 'react'
const MyContext = React.createContext()
const { Provider, Consumer } = MyContext;

function Child2() {
  const value = useContext(MyContext)
  return (
  <div>Child2: {value.foo}</div>
  )
}

export default function ContextTest() {
  return (
    <div>
      <Provider value={{foo: 'bar'}}>
        <Child2></Child2>
      </Provider>
    </div>
  )
}
```

#### 14.2.3 Class取值（类组件）

```react
import React from 'react'
const MyContext = React.createContext()
const { Provider, Consumer } = MyContext;

class Child3 extends React.Component {
  static contextType = MyContext
  render() {
    return (
      <div>Child3: {this.context.foo}</div>
    )
  }
}

export default function ContextTest() {
  return (
    <div>
      <Provider value={{foo: 'bar'}}>
        <Child3></Child3>
      </Provider>
    </div>
  )
}
```

### 14.3 总结

总共有三种方法取到Context的值，如下所示：

```react
import React, {useContext} from 'react'
const MyContext = React.createContext()
const { Provider, Consumer } = MyContext;

// Consumer取值
function Child(props) {
  return (
  <div>Child: {props.foo}</div>
  )
}

//  Hook取值
function Child2() {
  const value = useContext(MyContext)
  return (
  <div>Child2: {value.foo}</div>
  )
}

// 类的方式指定静态属性contextType，这样子上下文就会自动有个context
class Child3 extends React.Component {
  static contextType = MyContext
  render() {
    return (
      <div>Child3: {this.context.foo}</div>
    )
  }
}

export default function ContextTest() {
  return (
    <div>
      <Provider value={{foo: 'bar'}}>
        {/* Consumer包裹元素 */}
        <Consumer>
          { value => <Child {...value}></Child> }
        </Consumer>
        <Child2></Child2>
        <Child3></Child3>
      </Provider>
    </div>
  )
}
```



## 15. 

## 16. Redux的使用

![img](https://gitee.com/cai-lunduo/react-markdown-notes/raw/master/public/redux.png)

```node
cnpm install redux --save
```

### 16.1 简单使用

在文件store.js内：

```js
import {createStore} from 'redux'

const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case 'add': 
      return state + 1
    case 'minus':
      return state - 1
    default:
      return state
  }
}

const store = createStore(counterReducer)

export default store
```

我们通过`createStore`创建`Store`对象，参数为`Reducer`对象，`state`是当前`store`的属性值，可以是一个对象，在通过对`action`参数的`type`属性进行判断，修改`state`的值。

接下来我们在需要引用`store`里面的全局状态的组件引入`store`，在此之前我们先通过在控制台打印一下store到底是什么：

![image-20210331210840761](https://gitee.com/cai-lunduo/react-markdown-notes/raw/master/public/store.png)

如上所示，我们创建的`Store`对象包括了这么些个属性，我们可以通过`getState`方法获取state的值，也可以通过`dispatch`方法改变`state`的值，还有最后我们还要在页面渲染那里用`subscribe`进行订阅。

```react
import React from 'react'
import store from '../store'

// 原始Store组件
function StoreTest() {
  return (
    <>
    <div>{store.getState()}</div>
    <div>
      <button onClick={() => store.dispatch({type: 'add'})}>+</button>
      <button onClick={() => store.dispatch({type: 'minus'})}>-</button>
    </div>
    </>
  )
}
export default StoreTest
```

如上所示，我们可以通过`getState`获取到前面`store.js`的`state`值，再给<button>绑定点击事件，执行dispatch改变state的值。

订阅

```react
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import store from './store';

const render = () => {
  ReactDOM.render(
    <React.StrictMode>
      <App />
    </React.StrictMode>,
    document.getElementById('root')
  );
}
render()
store.subscribe(render)
```

只有订阅了，`store`状态才能在随着页面渲染时发生改变，不然你将看不到效果。

### 16.2 **思考**

上面的原始方法存在一个问题：在需要共享全局状态的组件，我们都需要引入store.js，这样子显得很繁杂，那么要如何改变这个问题呢？

还记得我们之前可以通过上下文进行隔代传值吗？是的，只要我们把`store`通过`Provider`传给根组件，其他组件自然也就可以不用引入`store.js`了。为此，react已经帮我们做好了，我们可以通过`react-redux`依赖来进行相应的操作。

```
cnpm install react-redux --save
```

### 16.3 react-redux

在`Provider`传入`store`

```react
// index.js
import store from './store';
import {Provider} from 'react-redux'

ReactDOM.render(
  <Provider store={store}>
    <React.StrictMode>
      <App/>
    </React.StrictMode>
  </Provider>,
  document.getElementById('root')
);
```

在需要用到`store`里面状态的组件里

```react
import React from 'react'
import store from '../store'
import {connect} from 'react-redux'


const mapStateToProps = state => ({num: state})

const mapDispatchToProps = {
    add: () => ({type: 'add'}),
    minus: () => ({type: 'minus'})
}

// 经过react-redux上下文处理的store组件
class StoreTest2 extends React.Component {
  render() {
    return (
      <>
        <div>{this.props.num}</div>
        <button onClick={() => this.props.add()}>+</button>
        <button onClick={() => this.props.minus()}>-</button>
      </>
    )
  }
}
export default connect(mapStateToProps, mapDispatchToProps)(StoreTest2)
```

在这里，我们使用了`connect`高阶组件，传入两个配置项，再对需要扩展的组件进行包装，注意这里的写法已经变了，逻辑已经被我们提到了外面，此时`ShowTest2`只是一个傻瓜式组件，不负责`dispatch`也不用调用`getState`方法，`connect`内部的两个配置项分别帮我们把`state`的值映射到被包装组件`StoreTest2`的内部，并放在`props`里面，这样子我们在组件内就可以直接通过`props`拿到`state`和`dispatch`了。

### **16.4 装饰器简化**

只是个语法糖

```react
@connect(mapStateToProps, mapDispatchToProps)
class StoreTest3 extends React.Component {
  render() {
    return (
      <>
        <div>{this.props.num}</div>
        <button onClick={() => this.props.add()}>+</button>
        <button onClick={() => this.props.minus()}>-</button>
      </>
    )
  }
}

export default StoreTest3
```

### **16.5 处理异步**

![image-20210401102441651](https://gitee.com/cai-lunduo/react-markdown-notes/raw/master/public/redux-thunk.png)

如图所示，在处理`action`的时候会先经过中间件层的处理，中间件会识别传进来的`action`到底是个对象还是函数，如果是函数则表明是异步请求，要先执行完才能继续往下一步进行。

**安装两个中间件**

```
npm install redux-thunk redux-logger -S
```

`redux-thunk`用来处理异步请求，`redux-logger`是一个日志打印中间件。

**使用**

```js
// store.js
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk'
import logger from 'redux-logger'


// reducer定义
...

// 中间件执行步骤：先logger再thunk
const store = createStore(counterReducer, applyMiddleware(logger, thunk))
export default store
```

需要进行异步操作的地方

```react
import React from 'react'
import store from '../store'
import {connect} from 'react-redux'

const mapStateToProps2 = state => ({num: state})
const mapDispatchToProps2 =  {
  add: () => ({type: 'add'}),
  minus: () => ({type: 'minus'}),
  asyncAdd: () => dispatch => {
    setTimeout(() => dispatch({type: 'add'}), 1000)
  }
}

@connect(mapStateToProps2, mapDispatchToProps2)
class StoreTest4 extends React.Component {
  render() {
    return (
      <>
        <div>{this.props.num}</div>
        <button onClick={() => this.props.add()}>+</button>
        <button onClick={() => this.props.minus()}>-</button>
        <button onClick={() => this.props.asyncAdd()}>+</button>
      </>
    )
  }
}

export default StoreTest4
```

这里新增了一个`asyncAdd`方法，注意这里的写法是返回一个带`dispatch`参数的函数，而不像其他同步操作直接返回对象。

### **16.6 代码抽离**

由于我们全局共享状态是有多种类型的，如用户的登录态、全局的主题、用户信息等，因此我们将所有全局共享状态都放置再store.js将会导致后期变得可维护差，为了使代码更具有可用性、易用性、可维护性。我们应该将每一部分的状态都放置在一个文件。

**步骤**

创建`store`文件夹，创建`index.js`为入口，将来通过`combineReducers`引入所有的状态，我们将上面的`counterReducer`和其相对于的`action creator`给分离到一个文件。

```js
// store/counter.redux.js
export const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case 'add': 
      return state + 1
    case 'minus':
      return state - 1
    default:
      return state
  }
}

// action creator
export const add = () => ({type: 'add'})
export const minus = () => ({type: 'minus'})
export const asyncAdd = () => dispatch => {
  setTimeout(() => dispatch({type: 'add'}), 1000)
}
```

在入口文件引入

```js
//  store/index.js
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk'
import logger from 'redux-logger'
import {counterReducer} from './counter.redux'

const store = createStore(
  counterReducer,
  applyMiddleware(logger, thunk)
)

export default store
```

这样就完成了简单的分离，接下来我们还要修改一下程序入口导入`store`的路径

```js
// index.js
import store from './store/index'
```

在需要用到`counter`状态的地方

```react
...
import { add, minus, asyncAdd } from '../store/counter.redux'

const mapStateToProps3 = state => ({num: state})
const mapDispatchToProps3 =  { add, minus, asyncAdd }

@connect(mapStateToProps3, mapDispatchToProps3)
class StoreTest5 extends React.Component {
  render() {
    return (
      <>
        <div>{this.props.num}</div>
        <button onClick={() => this.props.add()}>+</button>
        <button onClick={() => this.props.minus()}>-</button>
        <button onClick={() => this.props.asyncAdd()}>+</button>
      </>
    )
  }
}

export default StoreTest5
```

这里我们只需将对应的`action creator`导入，使得管理起来更加方便。实现了统一在store文件夹下的文件对所有共享状态进行管理，可维护性就瞬间提升了。





### 16.7 RTK

  除了Redux核心库外Redux还为我们提供了一种使用Redux的方式——Redux Toolkit。它的名字起的非常直白，Redux工具包，简称RTK。RTK可以帮助我们处理使用Redux过程中的重复性工作，简化Redux中的各种操作。

  安装，无论是RTK还是Redux，在React中使用时react-redux都是必不可少，所以使用RTK依然需要安装两个包：react-redux和@reduxjs/toolkit。

```
npm install react-redux @reduxjs/toolkit -S
```

#### CreateSlice

createSlice是一个全自动的创建reducer切片的方法，在它的内部调用就是createAction和createReducer，之所以先介绍那两个也是这个原因。createSlice需要一个对象作为参数，对象中通过不同的属性来指定reducer的配置信息。

```react
createSlice(configuration object)
```

配置对象中的属性：

`initialState` —— state的初始值

`name` —— reducer的名字，会作为action中type属性的前缀，不要重复

`reducers` —— reducer的具体方法，需要一个对象作为参数，可以以方法的形式添加reducer，RTK会自动生成action对象。

示例：

```react
const stuSlice= createSlice({
    name:'stu',
    initialState:{
        name: '孙悟空',
        age: 18,
        gender: '男',
        address: '花果山'
    },
    reducers:{
        setName(state, action){
            state.name = action.payload
        }
    }
});
```

> createSlice返回的并不是一个reducer对象而是一个slice对象（切片对象）。这个对象中我们需要使用的属性现在有两个一个叫做actions，一个叫做reducer。



#### Actions

切片对象会根据我们对象中的reducers方法来自动创建action对象，这些action对象会存储到切片对象actions属性中：

```react
stuSlice.actions; // {setName: ƒ}
```

上例中，我们仅仅指定一个reducer，所以actions中只有一个方法setName，可以通过解构赋值获取到切片中的action。

```react
const {setName} = stuSlice.actions;
```

开发中可以将这些取出的action对象作为组件向外部导出，导出其他组件就可以直接导入这些action，然后即可通过action来触发reducer。

#### Reducer

切片的reducer属性是切片根据我们传递的方法自动创建生成的reducer，需要将其作为reducer传递进configureStore的配置对象中以使其生效：

```react
const store = configureStore({
    reducer: {
        stu: stuSlice.reducer,
        school: schoolReducer
    }
});
```

总的来说，使用createSlice创建切片后，切片会自动根据配置对象生成action和reducer，action需要导出给调用处，调用处可以使用action作为dispatch的参数触发state的修改。reducer需要传递给configureStore以使其在仓库中生效。

#### 完整代码

app.js

```react
import ReactDOM from 'react-dom/client';
import App from "./App"
import {Provider} from "react-redux";
import store from "./store"

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
    <div>
        <Provider store={store}>
            <App/>
        </Provider>
    </div>
);
```

store/index.js

```react
import {configureStore} from "@reduxjs/toolkit";
import {stuReducer} from "./stuSlice"

// 创建store用来创建store对象,需要一个配置对象作为参数
const store = configureStore({
    reducer: {
        student:stuReducer
    }
})

export default store
```

store/stuSlice.js

```react
import {createSlice} from "@reduxjs/toolkit";

const stuSlice = createSlice({
    name: 'stu',
    initialState:{
        name:'孙悟空',
        age:18,
        gender: '男',
        address:'花果山'
    },
    reducers:{
        setName(state,action){
            state.name = action.payload
        },
        setAge(state,action){
            state.age = action.payload
        }
    }
})

export const {setName,setAge} = stuSlice.actions
export const {reducer:stuRecuder} = stuSlice
```

app.js

```react
import React from 'react'
import { useDispatch, useSelector} from "react-redux";
import {setName,setAge} from "./store/stuSlice"

const App = ()=> {
    // 获取state
    const {student} = useSelector(state=> state)
    // 派发修改state的reducers
    const dispatch = useDispatch()
    
    return (
    	<div>{student.name}</div>
        <button onClick={()=>dispatch(setName('猪八戒'))}>按钮</button>
    )
}
```



### 16.7 RTK Query

  RTK不仅帮助我们解决了state的问题，同时，它还为我们提供了RTK Query用来帮助我们处理数据加载的问题。RTK Query是一个强大的数据获取和缓存工具。在它的帮助下，Web应用中的加载变得十分简单，它使我们不再需要自己编写获取数据和缓存数据的逻辑。

  Web应用中加载数据时需要处理的问题：

1. 根据不同的加载状态显示不同UI组件
2. 减少对相同数据重复发送请求
3. 使用乐观更新，提升用户体验
4. 在用户与UI交互时，管理缓存的生命周期

  这些问题，RTKQ都可以帮助我们处理。首先，可以直接通过RTKQ向服务器发送请求加载数据，并且RTKQ会自动对数据进行缓存，避免重复发送不必要的请求。其次，RTKQ在发送请求时会根据请求不同的状态返回不同的值，我们可以通过这些值来监视请求发送的过程并随时中止。



#### 创建Api切片

RTKQ中将一组相关功能统一封装到一个Api对象中，比如：都是学生相关操作统一封装到StudentApi中，关于班级的相关操作封装到ClassApi中。接下来，我们尝试创建一个简单的Api，至于数据还是我们之前所熟悉的学生数据：

studentApi.js

```react
import {createApi, fetchBaseQuery} from "@reduxjs/toolkit/dist/query/react";

export const studentApi = createApi({
    reducerPath:'studentApi',
    baseQuery:fetchBaseQuery({
        baseUrl:'http://localhost:1337/api/',
        prepareHeaders:(headers, {getState}) => {
            const token = getState().store.token
            if(token){
                headers.set("Authorization", `Bearer ${token}`)
            }
            return headers
        } // 用来统一设置请求头
    }),
    endpoints(build) {
        return {
            getStudents: build.query({
                query() {
                    return 'students'
                }
            }),
        }
    }
});

export const {useGetStudentsQuery} = studentApi;
```

上例是一个比较简单的Api对象的例子，我们来分析一下，首先我们需要调用`createApi()`来创建Api对象。这个方法在RTK中存在两个版本，一个位于`@reduxjs/toolkit/dist/query`下，一个位于`@reduxjs/toolkit/dist/query/react`下。react目录下的版本会自动生成一个钩子，方便我们使用Api。如果不要钩子，可以引入query下的版本，当然我不建议你这么做。

`createApi()`需要一个配置对象作为参数，配置对象中的属性繁多，我们暂时介绍案例中用到的属性：

1. reducerPath

用来设置reducer的唯一标识，主要用来在创建store时指定action的type属性，如果不指定默认为api。

2. baseQuery

用来设置发送请求的工具，就是你是用什么发请求，RTKQ为我们提供了fetchBaseQuery作为查询工具，它对fetch进行了简单的封装，很方便，如果你不喜欢可以改用其他工具，这里暂时不做讨论。

3. fetchBaseQuery

简单封装过的fetch调用后会返回一个封装后的工具函数。需要一个配置对象作为参数，baseUrl表示Api请求的基本路径，指定后请求将会以该路径为基本路径。配置对象中其他属性暂不讨论。

4. endpoints

Api对象封装了一类功能，比如学生的增删改查，我们会统一封装到一个对象中。一类功能中的每一个具体功能我们可以称它是一个端点。endpoints用来对请求中的端点进行配置。

endpoints是一个回调函数，可以用普通方法的形式指定，也可以用箭头函数。回调函数中会收到一个build对象，使用build对象对点进行映射。回调函数的返回值是一个对象，Api对象中的所有端点都要在该对象中进行配置。

对象中属性名就是要实现的功能名，比如获取所有学生可以命名为getStudents，根据id获取学生可以命名为getStudentById。属性值要通过build对象创建，分两种情况：

查询：`build.query({})`

增删改：`build.mutation({})`

例如：

```react
getStudents: build.query({
    query() {
        return 'students'
    },
    // 用来转换响应数据的格式
    transformResponse(baseQueryReturnValue,meta){
        return baseQueryReturnValue.data
    },
    // 设置数据缓存的时间
    keepUnusedDataFor:0
}),
addStudent: build.mutation({
    query(stu){
        return {
            url: 'students',
            method: 'post',
            body: {data:stu}
        }
    }
})
```

先说query，query也需要一个配置对象作为参数（又他喵的是配置对象）。配置对象里同样有n多个属性，现在直说一个，query方法。注意不要搞混两个query，一个是build的query方法，一个是query方法配置对象中的属性，这个方法需要返回一个子路径，这个子路径将会和baseUrl拼接为一个完整的请求路径。比如：getStudets的最终请求地址是:

```
http://localhost:1337/api/`+`students`=`http://localhost:1337/api/students
```

可算是介绍完了，但是注意了这个只是最基本的配置。RTKQ功能非常强大，但是配置也比较麻烦。不过，熟了就好了。

上例中，我们创建一个Api对象studentApi，并且在对象中定义了一个getStudents方法用来查询所有的学生信息。如果我们使用react下的createApi，则其创建的Api对象中会自动生成钩子函数，钩子函数名字为`useXxxQuery`或`useXxxMutation`，中间的Xxx就是方法名，查询方法的后缀为Query，修改方法的后缀为Mutation。所以上例中，Api对象中会自动生成一个名为useGetStudentsQuery的钩子，我们可以获取并将钩子向外部暴露。

```react
export const {useGetStudentsQuery, useAddStudentMutation} = studentApi;
```

#### 创建Store对象

Api对象的使用有两种方式，一种是直接使用，一种是作为store中的一个reducer使用。store是我们比较熟悉的，所以先从store入手。

```react
import {configureStore} from "@reduxjs/toolkit";
import {studentApi} from "./studentApi";

export const store = configureStore({
    reducer:{
        [studentApi.reducerPath]:studentApi.reducer
    },
    middleware:getDefaultMiddleware =>
        getDefaultMiddleware().concat(studentApi.middleware),
});
```

创建store并没有什么特别，只是注意需要添加一个中间件，这个中间件已自动生成了我们直接引入即可，中间件用来处理Api的缓存。

store创建完毕同样要设置Provider标签，这里不再展示。接下来，我们来看看如果通过studentApi发送请求。由于我们已经将studentApi中的钩子函数向外部导出了，所以我们只需通过钩子函数即可自动加载到所有的学生信息。比如，现在在App.js中加载信息可以这样编写代码：

```react
import React from 'react';
import {useGetStudentsQuery,useAddStudentMutation} from './store/studentApi';

const App = () => {
    const {data, isFetching, isSuccess} = useGetStudentsQuery();
    const [addStudent, {isSuccess:isAddSuccess}] = useAddStudentMutation() // query返回的是对象，mutation返回数组
    // userQuery第一个参数可以传id等参数，可以接收一个对象作为第二个参数对请求配置
    const {data, isFetching, isSuccess} = useGetStudentsQuery(null,{
        pollingInterval:0, // 设置轮询间隔，单位毫秒，0为不轮询
        skip:false, // 设置是否跳过该请求
        refetchOnMountOrArgChange, // 设置是否每次重新加载数据
        refetchOnFocus:true, // 是否重新获取焦点时重载数据
        refetchOnReconnect:false // 是否断网重连后重新请求
        // 后两个请求都需要在store中配置setupListeners(store.dispatch)才能生效
    });

    return (
        <div>
            {isFetching && <p>数据正在加载...</p>}
            {isSuccess && data.data.map(item => <p key={item.id}>
                {item.attributes.name} --
                {item.attributes.age} --
                {item.attributes.gender} --
                {item.attributes.address}
            </p>)}
        </div>
    );
};

export default App;
```

直接调用useGetStudentsQuery()它会自动向服务器发送请求加载数据，并返回一个对象。这个对象中包括了很多属性：

1. data – 最新返回的数据
2. currentData – 当前的数据
3. error – 错误信息
4. isUninitialized – 如果为true则表示查询还没开始
5. isLoading – 为true时，表示请求正在第一次加载
6. isFetching 为true时，表示请求正在加载
7. isSuccess 为true时，表示请求发送成功
8. isError 为true时，表示请求有错误
9. refetch 函数，用来重新加载数据





## React-router5

### 安装

npm

```
npm install react-router-dom@5 -S
```

yarn

```
yarn add react-router-dom@5
```

### 起步

`index.js`

```react
// ... 其他包导入
import {BrowserRouter} from 'react-router-dom'

ReactDOM.render(
  <BrowserRouter>
    <Provider store={store}>
      <React.StrictMode>
        <App/>
      </React.StrictMode>
    </Provider>
  </BrowserRouter>,
  document.getElementById('root')
);
```

如上所示，我们需要给根组件包裹一个`BrowserRouter`标签，这样子我们就可以在浏览器使用路由了。

在`react`中，路由即组件，简单用法如下：

```react
import { Link, Route } from 'react-router-dom'
function About() {
  return (<div>关于页</div>)
}

function Home() {
  return (<div>首页</div>)
}

export default function RouteTest() {
  return (
    <div>
      <ul>
        <Link to='/'>Home</Link>
        <Link to='/about'>About</Link>
      </ul>
      <div>
        <Route exact path='/' component={Home}></Route>
        <Route path='/about' component={About}></Route>
      </div>
    </div>
  )
}
```

- 默认`Route`的`path`是模糊匹配，加上`exact`才是严格匹配
- `react-route`不要用`a`标签进行跳转，会刷新页面重新发送请求

- `Link`作用类似于`a`标签，会在页面展示让用户点击，而`Route`就是一个真正的路由映射组件，如上所示，`path`指定访问路径，`component`指定要展示的组件。其中因为`react-router`的路由包容性（只要path匹配到，就会渲染），我们还需对`path`为`/`的路由添加一个`exact`字段，这样子就只会精准匹配了。
- `NavLink`和`Link`作用相似，但是`NavLink`可以指定激活路由的样式





### Router组件

被`Route`标签包裹的组件，传入的值就不是`props`了，而是一个路由器对象，我们可以打印看看路由器对象到底长啥样

![image-20210401165044914](https://gitee.com/cai-lunduo/react-markdown-notes/raw/master/public/router.png)

如上所示，有`history`、`location`、`match`三个主要属性

```js
  // 1.history: 导航指令
  // 2.match: 获取参数信息
  // 3.location: 当前url信息
```

route组件是路由的映射组件，通过该组件将url地址和React组件进行映射，映射后当url地址变为指定地址时指定的组件就会显示，否则不显示。

```react
<Route path="/home" component={Home} />
<Route path="/about" component={About} />
```

上例中，路径`/home`和`<Home/>`组件进行了映射，路径`/about`和`<About/>`组件进行了映射。当访问`http://localhost:3000/about`时，about组件会自动渲染显示，访问`http://localhost:3000/home`时，home组件会自动渲染显示。

Route组件可以设置以下几个属性

1. path
2. exact
3. strict
4. component
5. render
6. children
7. location
8. sensitive

#### path

用来设置要映射的路径，可以是一个字符串或字符串数组。字符串用来匹配单个路径，数组可以匹配多个路径。看一个数组的例子：

```react
<Route path={["/about", "/hello"]}>
    <About/>
</Route>
```

使用数组映射后，当我们访问数组中的路径时都会使组件挂载。设置路径时也可以在路径中设置参数，比如：`/student/:id`其中id就是一个参数，可以动态的传递`:id`的值，换句话说`/student/1`或`/student/2`，都会触发组件挂载。

设置动态参数后，在组件的内部可以使用`useParams()`钩子来读取参数：

```react
const Student = () => {
    const {id} = useParams();
    return <div>学生id：{id}</div>
};

...略...
<Route path="/student/:id">
    <Student/>
</Route>
...略...
```

#### exact

路由的匹配默认并不是完整匹配，这意味着路由映射的地址是`/home`时，只要我们访问的路径是以`/home`开头就会触发组件的挂载，默认情况下是不会检查路由的子路径的。比如：`/home/hello`、`/home/abc`等都会导致home组件挂载。

exact属性用来设置路由地址是否完整匹配，它需要一个布尔值，默认为false，就像上边的情况。如果设置为true，那么只有地址和path完全一致时，组件才会挂载。

```react
<Route path="/home" exact>
    <Home/>
</Route>
```

这样一来只有访问`/home`时，home组件才会挂载，差一个字母都不行哦！

#### strict

布尔值，默认值为false。false时，会匹配到以`/`结尾的路径。比如：path设置为`/home`默认情况下`/home/`也会导致组件挂载。设置为true时，以`/`结尾的路径不会被匹配。

#### component

设置路径匹配后需要挂载的组件。作用和Route的标签体类似。

```react
<Route path="/home" component={Home}/>
```

和标签体指定组件不同，如果通过component属性指定组件，React Router会自动向组件中传递三个参数match、location和history。

match

对象，表示请求匹配的路径信息，其中包含四个属性：

1. param —— 请求参数
2. isExact —— 布尔值，请求路径是否完整匹配
3. path —— 请求路径的规则
4. url —— 匹配到的url地址

#### location

对象，表示浏览器地址栏的信息，请求完整路径、查询字符串等，可能具有的属性：

1. pathname —— 请求的路径
2. search —— 查询字符串
3. hash —— hash字符串
4. state —— 历史记录中的状态对象，可以用来在跳转时传递数据

#### history

对象，用来读取和操作浏览器的历史记录（页面跳转）等功能，属性：

1. length —— 历史记录的数量
2. action —— 当前历史记录的状态，pop（前进、后退、新记录创建、索引发生变化）；push（新记录添加）；replace（历史记录被替换）
3. location —— location对象
4. push() —— 添加新的历史记录
5. replace() —— 替换历史记录
6. go() —— 跳转到指定记录
7. goBack() —— 回退
8. goForward() —— 前进
9. block() —— 用来阻止用户跳转行为，可以用Prompt组件代替

#### render

render也是Route组件中的属性，和component类似，也用来指定路径匹配后需要挂载的组件。只是render需要的是一个回调函数作为参数，组件挂载时，render对应的回调函数会被调用，且函数的返回值会成为被挂载的组件。render的回调函数中会接收到一个对象作为参数，对象中包含三个属性，即match、location和history，我们可以根据需要选择是否将其传递给组件。

```react
<Route path="/student/:id" render={routeProps => <Student {...routeProps}/>} />
```

#### children

children实际上就是组件的组件体，设置方式有两种一个是通过组件体设置，一个是通过children属性设置。它的值也有两种方式，一种直接传递组件，这样当路径匹配时组件会自动挂载。一种是传递一个回调函数，这样它和render的特点是一样的。

直接设置组件：

```react
<Route path="/student/:id" children={<Student/>} />
<Route path="/student/:id">
    <Student/>
</Route>
```

传递回调函数：

```react
<Route path="/student/:id" children={routeProps => <Student {...routeProps}/>} />
<Route path="/student/:id">
    {routeProps => <Student {...routeProps}/>}
</Route>
```

需要注意的时，当children接收到的是一个回调函数时，即使路径没有匹配组件也会被挂载到页面中（没有使用Switch标签的情况下），这一特性可以在一些特殊应用场景下发挥作用。如果不希望出现路径不匹配时组件被挂载的情况，最好选择使用render来代替。





### Switch

`react`路由是包容性的，也就是匹配到的路由都会被展示出来。但是我们可以通过`Switch`标签让`react`路由不再具有包容性，只展示匹配到的第一个路由：

```react
import { Link, Route, Switch, NavLink } from 'react-router-dom'
import { BrowserRouter } from 'react-router-dom'

function About(router) {
  return <div>about</div>
}

function Home() {
  return <div>home</div>
}

function NotFound() {
  return (
    <div>
      <h2>not found</h2>
    </div>
  )
}

function App() {
  return (
    <div className="App">
      <BrowserRouter>
        {/* 内联样式 */}
        <div>
          <NavLink activeStyle={{color: 'blue'}} to="/home">Home</NavLink>
          <NavLink activeStyle={{color: 'red'}} to="/about">about</NavLink>
        </div>
        {/* 一个switch为一组，只会展示第一个被匹配的组件 */}
        <Switch>
          <Route component={Home} path="/home"></Route>
          <Route component={About} path="/about"></Route>
          <Route component={NotFound}></Route>
        </Switch>
        
      </BrowserRouter>
    </div>
  )
}

```

如上所示，我们让`Switch`包裹的路由为一组，在这一组中只匹配第一个匹配到的路由则停止继续匹配，可以注意到我们在最后放了一个NotFound路由，这也是路由找不到时可以写的写法，因为没有`path`，前面的匹配不到则`NotFound`一定会被匹配到。

### Redirect

redirect就是重定向组件，有一个from和to属性

常用基础写法如下

```react
{/* 将from 定向到 to */}
<Redirect from="/abc" to='/about'></Redirect>

<Redirect to='/home'></Redirect>

<Route path="/info" render={() => {
	return login ? <Info/> : <Redirect to="/home"></Redirect>
}}></Route>
```



### 其他组件

#### HashRouter

除了BrowserRouter以外，react router中还为我们提供了HashRouter，它是干什么用的呢？其实很简单，当我们使用BrowserRouter时，路径会直接根据url地址进行跳转，也就是我们在使用应用时在浏览器的地址栏看到的地址就和我们正常去访问网页一样。

但是，HashRouter不是这样，使用HashRouter时，组件的跳转不再是以完整的url形式，而是通过url地址中的hash值进行跳转（url地址中#后的内容为hash值）。

BrowserRouter的地址栏

![img](https://my-wp.oss-cn-beijing.aliyuncs.com/wp-content/uploads/2022/06/20220609180542950.png)

HashRouter的地址栏

![img](https://my-wp.oss-cn-beijing.aliyuncs.com/wp-content/uploads/2022/06/20220609180515252.png)

为什么会有这两种Router呢？首先，你要明确我们的项目在开发完成后需要进行构建，构建后的代码需要放到服务器中，以供用户访问。服务器无非就是Nginx或Apache这些东西，服务器的主要功能是将url地址和网页进行映射。传统web项目中，每一个页面都对应一个文件，当用户访问/index.html时，服务器会自动返回根目录下的index.html。当用户访问/about.html时，服务器会返回根目录下about.html。换句话说url和文件的映射都是由服务器来完成的。

但是React项目不同，React项目所有的页面都是通过React进行渲染构建的。项目中只存在一个index.html没有那么多的页面（所以才叫单页应用）。当浏览器地址发生变化时，比如用户访问/about时，此时是不需要服务器介入的，react router会自动挂载对应的组件。

当我们将React项目部署到服务器时，如果直接访问根目录，请求会直接发送给index.html。这个页面我们是有的，所以此时不会有任何问题。用户访问页面后，点击页面后的连接切换到不同的组件也没有问题，因为页面并没有真的发生跳转，而是通过react router在内存中完成了模拟跳转。但是，当我们刷新某个路由或直接通过浏览器地址栏访问某个路由时，比如：http://localhost:3000/about，此时请求会发送给服务器，服务器会寻找名为about的资源（此时并没有经过React）。显然找不到这个资源，于是返回404。

这样一来，我们的项目只能够通过首页访问，然后点击链接跳转，刷新和直接通过路由访问都是不行的，一旦进行这些操作就会出现404。

怎么办呢？两种解决方式：

1. 使用HashRouter，HashRouter通过hash地址跳转，而服务器不会处理hash地址，这样地址就会交由React处理，路由便可正常跳转。缺点是url地址上总会多出一个#，但不妨碍使用。
2. 修改服务器映射规则，将所有的请求交给React处理，禁止服务器自动匹配页面。以nginx为例，可以将`nginx.conf`中的配置信息修改如下：

```
location / {
    root   html;
    try_files $uri /index.html;
}
```

两种方式都可以解决404的问题，具体采用那种方案，需要根据你自己项目的实际情况选择。

#### NavLink

特殊版本的Link，可以根据不同的情况设置不同的样式。

属性：

1. activeClassName —— 字符串 链接激活时的class
2. activeStyle —— 对象 链接激活时的样式
3. isActive —— 函数，可动态判断链接是否激活
4. style —— 函数，动态设置样式
5. className —— 函数，动态设置class值

#### Prompt

prompt组件可以在用户离开页面前弹出提示。

属性：

1. message 字符串/函数，设置离开前显示的提示信息
2. when布尔值，设置是否显示提示

#### Redirect

将请求重定向到一个新的位置，经常用来进行权限的处理。例如：当用户已经登录时则正常显示组件，用户没有登录时则跳转到登录页面。

```
{isLogin && <SomeAuthComponent/>}
{!isLogin && <Redirect to={"/login"}></Redirect>}
```

上例中，如果isLogin的值为true，表示用户已经登录，若用户登录，则挂载对应组件。若isLogin值为false，则挂载Redirect组件触发重定向，重定向会使得路径跳转到登录页面。

属性：

1. to —— 重定向的目标地址，可以是一个字符串也可以是一个对象
2. from —— 需要重定向的地址
3. push —— 布尔值，是否使用push方式对请求进行重定向

#### 钩子函数

1. useHistory
2. useLocation
3. useParams
4. useRouteMatch





### 子路由与动态路由

#### 子路由

子路由部分包括动态路由，那么在`react`中如何编写子路由呢？

举个例子，比如我们想要在上面的`About`页嵌套子路由，写法可以参考下面

```react
function About(params) {
  return (
    <div>
      <h3>个人中心</h3>
      <div>
        <Link to="/about/me">个人信息</Link>
        <Link to="/about/order">订单查询</Link>
      </div>
      <Switch>
        <Route path="/about/me" component={() => <div>Me</div>} />
        <Route path="/about/order" component={() => <div>order</div>} />
        <Redirect to="/about/me" />
      </Switch>
    </div>
  );
}
```

很简单，只要在`About`组件嵌套路由即可。

但是需要注意一点：

写子路由时父亲路由不能开启路由严格模式，即不能在父组件加个exact属性，否则一旦开启严格模式，子路由将不会被路由系统找到，从而匹配不到，如：

```react
import { Link, Route, Switch, NavLink, Redirect } from 'react-router-dom'
import { BrowserRouter } from 'react-router-dom'

function AAA() {
  return (<h2>aaa</h2>)
}
function BBB() {
  return (<h2>bbb</h2>)
}

function About(router) {
  return (<div>
    <Link to="/about/aaa">a</Link>
    <Link to="/about/bbb">b</Link>

    <Route path="/about/aaa" component={AAA}></Route>
    <Route path="/about/bbb" component={BBB}></Route>
  </div>)
}

function Home() {
  return <div>home</div>
}


function App() {
  return (
    <div className="App">
      <BrowserRouter>
        {/* 内联样式 */}
        <div>
          <NavLink activeStyle={{color: 'blue'}} to="/home">Home</NavLink>
          <NavLink activeStyle={{color: 'red'}} to="/about">about</NavLink>
        </div>
        {/* 一个switch为一组，只会展示第一个被匹配的组件 */}
        <Switch>
          <Route exact component={Home} path="/home"></Route>
          <Route exact component={About} path="/about"></Route>
        </Switch>
        
      </BrowserRouter>
    </div>
  )
}

export default App
```

在这里我们对`/about`路由开启了严格模式，而`About`组件下有子组件，但是我们会发现访问子组件的时候，子组件并没有展示出来，这是因为`/about`路由开启了严格模式从而导致子路由匹配不到。

#### 动态路由

动态路由对于在`url`传值的很简单，使用`:参数`即可，如果要通过`query`传值可以在`Route`加上相应的属性值，然后在对应组件的参数`路由对象`中的`match`字段便可拿到

```react
import {Link, Route, Switch, Redirect} from 'react-router-dom'

function Detail(props) {
  return (<div>
    <ul>
      <li></li>
    </ul>
    详情页：{props.match.params.id}
  </div>)
}

<Link to='/detail'>Detail</Link>
<Route path='/detail/:id' component={Detail}></Route>
```

### 传参

react传参可以通过三种方式，可以通过params、query或者state对象进行传参

```react
import { Link, Route, Switch, NavLink, Redirect } from 'react-router-dom'
import { BrowserRouter } from 'react-router-dom'
import { useState } from 'react'
import qs from 'querystring'


/**
 * 路由传参
 */


function Home(props) {
  let [data] = useState([
    {id: 1, title: '新闻'},
    {id: 2, title: '财经'},
    {id: 3, title: '体育'},
    {id: 4, title: '文化'},
  ])
  return (<div>
    <ul>
      {/* params传参 /home/:id/:title */}
      {/* {
        data.map(item => <Link key={item.id} to={`${props.match.path}/total/${item.id}/${item.title}`} >{item.title}</Link>)
      } */}
      {/* query传参 /home?id=1&title=weibo */}
      {/* {
        data.map(item => <Link key={item.id} to={`${props.match.path}/total?id=${item.id}&title=${item.title}`} >{item.title}</Link>)
      } */}
      {/* state参数 */}
      {
        data.map(item => <Link key={item.id} to={{pathname: `${props.match.path}/total`, state: {id: item.id, title: item.title} }} >{item.title}</Link>)
      }
      <Route path={`${props.match.path}/total`} component={Total}></Route>
    </ul>
  </div>)
}

// 子组件
function Total(props) {
  console.log(props)
  // params取值
  // let {id, title} = props.match.params
  // query取值
  // let queryStr = props.location.search
  // let queryObj = qs.parse(queryStr.slice(1))
  // console.log(queryObj)
  // state参数取值
  let stateObj = props.location.state
  console.log(stateObj)

  return (
    <div>total</div>
  )
}


function App() {
  return (
    <div className="App">
      <BrowserRouter>
        {/* 内联样式 */}
        <div>
          <NavLink activeStyle={{color: 'blue'}} to="/home">Home</NavLink>
        </div>
        {/* 一个switch为一组，只会展示第一个被匹配的组件 */}
        <Switch>
          <Route component={Home} path="/home"></Route>
        </Switch>
        
      </BrowserRouter>
    </div>
  )
}

export default App

```





### 路由导航守卫(重点)

路由导航守卫是我们编写路由不可避免的一个部分，那么我们在`react`如何编写路由导航呢？

**答案：通过高阶组件扩展实现**

现在我想实现一个常见的功能：访问`About`页的时候判断用户是否处于登录态，如果是登录态则让他访问`About`页，如果没登陆则重定向到`Login`页。

我们一步步来，既然我们要判断用户是否处于登录态，由于登录态需要全局共享，我们需要用到`redux`，则我们可以在`store`下创建一个`user.redux.js`，管理用户的登录状态，注意，我们`login`动作也放在了这里，将来使用需要导入。

```react
const initial = {
  isLogin: false,
  loading: false,
};

export default (state = initial, action) => {
  switch (action.type) {
    case 'requestLogin':
      return {
        isLogin: false,
        loading: true,
      }
    case 'login': 
      return {
        isLogin: true,
        loading: false,
      }
    default:
      return state
  }
}

// action creator
export const login = () => dispatch => {
  dispatch({type: 'requestLogin'})
  setTimeout(() => {
    dispatch({type: 'login'})
  }, 3000)
}
```

在`store/index.js`下引入这个`Reducer`，并通过`combineReducers`来合并所有的`reducer`

```react
// store/index.js
import {createStore, applyMiddleware, combineReducers} from 'redux'
import thunk from 'redux-thunk'
import logger from 'redux-logger'
import {counterReducer} from './counter.redux'
import user from './user.redux'

const store = createStore(
  combineReducers({counterReducer, user}),
  applyMiddleware(logger, thunk)
)

export default store
```

**第一步完成**，我们已经将用户的登录态放在全局进行管理。

接下来，我们先要创建一个自定义的<PrivateRoute>组件，内部其实也是返回<Route>，但是我们将要对其功能进行增强。

```react
function PrivateRoute({ component:Comp, isLogin, ...rest }) {
  return (
    <Route 
    {...rest} 
    render={props => 
        isLogin ? (<Comp/>) : <Redirect to={{ 
          pathname: '/login', 
          state: { redirect: props.location.pathname} 
        }}/> 
      }
    />
  )
}
```

在这里我们做了这些工作：

```js
1. 解构props为component, isLogin, ...rest
单独解构出component的目的：
	1. 避免component属性传入Route，不然render将不生效！因为component优先级比render高
	2. 给component重命名，因为react里面的组件是大写字母开头的
2. isLogin怎么来的？
	我们等下会通过connect工厂函数链接react和redux，映射用户的登录状态到PrivateRoute的props中
3. 为什么要...rest?
	因为我们原本的<Route/>要接收path等相关参数，我们在这里直接把这些参数搬过来放在我们将要返回的组件中
4. 返回全新组件<Route/>，在这个组件中，render是一个接收props的函数，我们在这判断用户的登录态，并返回不同的组件
	1.如果用户已登录，则返回用户访问的About页面对应的组件
	2.否则redirect到login页面,并给to对象里面设置一个state.redirect属性，将来登陆成功则自动定向到这个redirect去
```

`Login`组件

```react
function Login({location, isLogin, login}) {

  const redirect = location.state && location.state.redirect || "/";

  if(isLogin) {
    return <Redirect path={redirect}/>
  }
  return (<div><button onClick={() => login()}>登录</button></div>)
}
```

这里我们从`location`拿到重定向的地址，为将来登陆成功时指定回跳地址。

至此我们**第二步完成**了。

现在**最后一步**就是将全局的用户登录态给注入`Login`和`PrivateRoute`中，通过

`connect(配置项)(被加工组件)`的方式注入

```react
// ...其他包导入
import {login} from '../store/user.redux'
import {connect} from 'react-redux'

const Login = connect(
  state => ({isLogin:state.user.isLogin}),
  {login}
 )(function({location, isLogin, login}) {

  const redirect = location.state && location.state.redirect || "/";

  if(isLogin) {
    return <Redirect path={redirect}/>
  }
  return (<div><button onClick={() => login()}>登录</button></div>)
})

// 路由导航守卫
const PrivateRoute = connect(state => ({isLogin: state.user.isLogin}))(({ component:Comp, isLogin, ...rest }) => {
  return (
    <Route 
    {...rest} 
    render={props => 
        isLogin ? (<Comp/>) : <Redirect to={{ 
          pathname: '/login', 
          state: { redirect: props.location.pathname} 
        }}/> 
      }
    />
  )
})

```

用法：

```react
<PrivateRoute path='/about' component={About}></PrivateRoute>
```



## React-router6

### 安装

npm

```
npm install react-router-dom@6 -S
```

yarn

```
yarn add react-router-dom@6
```



### 5和6的区别

| 条目       | v5                    | v6                    |
| ---------- | --------------------- | --------------------- |
| 声明路由   | <Switch>              | <Routes>              |
| 路由管理   | <Route companent={}/> | <Route element={}/>   |
| 编程式导航 | useHistory()          | useNavigate()         |
| 跳转       | <Redirect to="path"/> | <Navigate to="path"/> |
| 严格匹配   | exact需要声明         | exact默认             |
| 路径规则   | 绝对路径/404          | 相对路径*             |



### Routes组件

和版本5不同，6中的Route组件不能单独使用，而是必须要放到Routes组件中。简言之Routes就是一个存放Route的容器。

### Route组件

Route作用和版本5的一样，只是变得更简单了，没有了那么多复杂的属性，并且Route组件必须放到Routes中，当浏览器的地址发生变化时，会自动对Routes中的所有Route进行匹配，匹配到的则显示，其余Route则不再继续匹配。可以将Route当成是一个类似于if语句的东西，路径（path）匹配则其中的组件便会被渲染。

1. path —— 要匹配的路径
2. element —— 路径匹配后挂载的组件，直接传JSX
3. index —— 布尔值，路由是否作为默认组件显示

### Outlet组件

Outlet组件用来在父级路由中挂载子路由。

在版本6中Route组件是可以嵌套的，可以通过嵌套Route来构建出嵌套路由，像这样：

```react
<Route path='/students' element={<StudentList/>}>
    <Route path=':id' element={<Student/>}/>
</Route>
```

上例中，Route嵌套后，如果访问`/students`则会挂载StudentList组件，如果访问`/students/:id`则会自动在StudentList组件中对Student组件进行挂载。在StudentList组件中就可以使用Outlet来引用这些被挂载的组件。

```react
const StudentList = () => {
    return <div>
        学生列表
        <Outlet/>
    </div>
};
```

### Link组件

和版本5的类似，具体区别看视频

### NavLink组件

和版本5的类似

```react
<NavLink style={({isActive}) => {
        return isActive? {color:"red"}: null
    }} to="/about"></NavLink>
```

### Navigate

类似于版本5中的Redirect组件，用来重定向跳转页面

```react
<Navigate to="/about" replace state={{preLocation: location}}/>
```

### 部分钩子函数

1. useLocation —— 获取地址信息的钩子

```react
{
    hash:""
    key: ""
    pathname: "/" // 当前要访问的路径
    search: ""
    state: null // navigate传的数据
}
```

2. useNavigate —— 获取跳转页面函数的钩子

```react
const nav = useNavigate()
nav('/about', {replace:true})
```

3. useParams —— 获取请求参数

4. useMatch —— 检查路径是否匹配某个路由

```react
const match = useMatch('/about') 
// 如果路由匹配返回一个对象，不匹配返回null
```





## redux-saga

`redux-saga` 是一个用于管理应用程序 Side Effect（副作用，例如异步获取数据，访问浏览器缓存等）的 library，它的目标是让副作用管理更容易，执行更高效，测试更简单，在处理故障时更容易。

`redux-saga`内部用到了ES6的Generator功能，使得我们异步流程可以像简单的操作同步代码一样（步骤看起来像async/await），但是Generator的功能比async/await更加强大，同时可测试性还增强了。

初级教程：https://redux-saga-in-chinese.js.org/docs/introduction/BeginnerTutorial.html

如果对`redux-saga`没有过了解，请点击上述链接到官方文档去看入门教程，写的非常详细也很容易明白。

你可能已经用了 `redux-thunk` 来处理数据的读取。不同于 redux thunk，你不会再遇到回调地狱了，你可以很容易地测试异步流程并保持你的 action 是干净的。

接下来我会以一个用户登录验证的功能来讲述`redux-saga`。

`store/user.redux.js`，redux中用户的登录态，我们给state设置三个状态，以此来判断用户的登录状态和展示错误信息。

```js
const initial = {
  isLogin: false,
  loading: false,
  error: ''
};

export default (state = initial, action) => {
  switch (action.type) {
    case 'requestLogin':
      return {
        isLogin: false,
        loading: true,
        error: ''
      }
    case 'login': 
      return {
        isLogin: true,
        loading: false,
        error: ''
      }
    case 'requestError':
      return {
        isLogin: false,
        loading: false,
        error: action.message
      }
    default:
      return state
  }
}

// action creator for redux-thunk
// export const login = () => dispatch => {
//   dispatch({type: 'requestLogin'})
//   setTimeout(() => {
//     dispatch({type: 'login'})
//   }, 3000)
// }

export function login(uname) {
  return { type: 'login', uname}
}
```

> 上面代码中， 被注释的代码是以前写`redux-thunk`的时候所遗留的，可以观察到`redux-thunk`他在执行异步函数时需要返回一个函数，而在`redux-saga`中，只需返回一个plain Object对象即可，（plain Object：纯粹的对象（通过 "{}" 或者 "new Object" 创建的））

接下来我们编写sagas.js

```react
import { put, call, takeEvery } from 'redux-saga/effects'

const UserService = {
  login(uname) {
    return new Promise((resolve, reject) => {
      // 模拟异步
      setTimeout(() => {
        if(uname === 'cai') {
          resolve({id:1, name:'cai', mobile:'13226752873'})
        }else {
          reject('用户名或密码错误')
        }
      },2000)
    })
  }
}

function* login(action) {
  try {
    // 标识开始请求
    yield put({type: 'requestLogin'})
    const result = yield call(UserService.login, action.uname)
    // 登录成功
    yield put({type: 'login', result})
  } catch(message) {
      // 登录失败，将错误信息传入action，让user的reducer能够接收，为后续map到页面的props做准备
    yield put({type: 'requestError', message})
  }
}

function* mySaga() {
  yield takeEvery('login', login)
}


export default mySaga
```

如上所示，我们使用了`put`、`call`、`takeEvery`工具函数，按我个人理解：

> call : 用来通知saga中间件执行异步函数的工具方法，最后会拿到异步函数执行后返回的数据
>
> put : 用来通知saga中间件向redux进行dispatch操作，是同步的
>
> takeEvery : 用来监听action，相当于进行了一层拦截， 然后再进行自己定义的操作，如上例所示，我们监听了login的调用，一旦login函数被触发，则会被takeEvery拦截到，然后进行我们自己定义的Generator，也就是function* login(action) 。
>
> 需要注意一点的是，dispatch并不由call和put派发，而是通知saga中间件让他进行dispatch

接下来在`store`的入口文件对中间件进行注册和run一下我们自定义的mySaga，因为这样子`takeEvery`才可以在全局对`login`事件进行监听并派发相应动作。

```react
// 其他包导入
import user from './user.redux'
import createSagaMiddleware from 'redux-saga'
import MySaga from './sagas'

// 1. 创建saga中间件
const sagaMiddleware = createSagaMiddleware()
const store = createStore(
  combineReducers({..., user}),
  // 2. 注册saga中间件
  applyMiddleware(sagaMiddleware)
)
// 3. 让我们的saga在全局监听
sagaMiddleware.run(MySaga)

export default store
```

在登录组件

```js
const LoginForSaga = connect(
  state => ({
    // 将user的redux给map到当前组件的props
    isLogin: state.user.isLogin,
    error: state.user.error
  }),
  {login}
  )(function({location, isLogin, login, error}) {

  const redirect = location.state && location.state.redirect || "/";
  const [uname, setUname] = useState('')

  if(isLogin) {
    return <Redirect path={redirect}/>
  }
  return (
    <div>
      {/* 错误信息展示 */}
      {error && <p>{error}</p>}
      <input type="text" onChange={e => setUname(e.target.value)}/>
      {/* redux-saga */}
      <button onClick={() => login(uname)}>登录</button>
    </div>)
})
```

## umi版本2

参考文档：https://umijs.org/docs

### 介绍

Umi，中文可发音为**乌米**，是可扩展的企业级前端应用框架。Umi 以路由为基础的，同时支持配置式路由和约定式路由，保证路由的功能完备，并以此进行功能扩展。然后配以生命周期完善的插件体系，覆盖从源码到构建产物的每个生命周期，支持各种功能扩展和业务需求。

Umi 是蚂蚁金服的底层前端框架，已直接或间接地服务了 3000+ 应用，包括 java、node、H5 无线、离线（Hybrid）应用、纯前端 assets 应用、CMS 应用等。他已经很好地服务了我们的内部用户，同时希望他也能服务好外部用户。

### 特性

- **开箱即用**：内置 SSR，一键开启，`umi dev` 即 SSR 预览，开发调试方便。

- **服务端框架无关**：Umi 不耦合服务端框架（例如 [Egg.js](https://eggjs.org/)、[Express](https://expressjs.com/)、[Koa](https://koajs.com/)），无论是哪种框架或者 Serverless 模式，都可以非常简单进行集成。

- **支持应用和页面级数据预获取**：Umi 3 中延续了 Umi 2 中的页面数据预获取（getInitialProps），来解决之前全局数据的获取问题。

- **支持按需加载**：按需加载 `dynamicImport` 开启后，Umi 3 中会根据不同路由加载对应的资源文件（css/js）。

- **内置预渲染功能**：Umi 3 中内置了预渲染功能，不再通过安装额外插件使用，同时开启 `ssr` 和 `exportStatic`，在 `umi build` 构建时会编译出渲染后的 HTML。

- **支持渲染降级**：优先使用 SSR，如果服务端渲染失败，自动降级为客户端渲染（CSR），不影响正常业务流程。

- **支持流式渲染**：`ssr: { mode: 'stream' }` 即可开启流式渲染，流式 SSR 较正常 SSR 有更少的 [TTFB](https://baike.baidu.com/item/TTFB)（发出页面请求到接收到应答数据第一个字节所花费的毫秒数） 时间。

- **兼容客户端动态加载**：在 Umi 2 中同时使用 SSR 和 dynamicImport（动态加载）会有一些问题，在 Umi 3 中可同时开启使用。

- **SSR 功能插件化**：Umi 3 内置的 SSR 功能基本够用，若不满足需求或者想自定义渲染方法，可通过提供的 API 来自定义。

  ![image-20210401165044914](https://gitee.com/cai-lunduo/react-markdown-notes/raw/master/public/umi.png)

  ![image-20210401165044914](https://gitee.com/cai-lunduo/react-markdown-notes/raw/master/public/umi-flow.png)

Dva 是基于 React + Redux + Saga 的最佳实践沉淀, 做了 3 件很重要的事情, 大大提升了编码体验:

1. 把 store 及 saga 统一为一个 `model` 的概念, 写在一个 js 文件里面

2. 增加了一个 Subscriptions, 用于收集其他来源的 action, eg: 键盘操作

3. model 写法很简约, 类似于 DSL 或者 RoR, coding 快得飞起✈️

### 与其他框架对比

create-react-app 是基于 webpack 的打包层方案，包含 build、dev、lint 等，他在打包层把体验做到了极致，但是不包含路由，不是框架，也不支持配置。所以，如果大家想基于他修改部分配置，或者希望在打包层之外也做技术收敛时，就会遇到困难。

next.js 是个很好的选择，Umi 很多功能是参考 next.js 做的。要说有哪些地方不如 Umi，我觉得可能是不够贴近业务，不够接地气。比如 antd、dva 的深度整合，比如国际化、权限、数据流、配置式路由、补丁方案、自动化 external 方面等等一线开发者才会遇到的问题。

### dva与umi的约定

```elm
.
├── dist/                          // 默认的 build 输出目录
├── mock/                          // mock 文件所在目录，基于 express
├── config/
    ├── config.js                  // umi 配置，同 .umirc.js，二选一
└── src/                           // 源码目录，可选
    ├── layouts/index.js           // 全局布局
    ├── pages/                     // 页面目录，里面的文件即路由
        ├── .umi/                  // dev 临时目录，需添加到 .gitignore
        ├── .umi-production/       // build 临时目录，会自动删除
        ├── document.ejs           // HTML 模板
        ├── 404.js                 // 404 页面
        ├── page1.js               // 页面 1，任意命名，导出 react 组件
        ├── page1.test.js          // 用例文件，umi test 会匹配所有 .test.js 和 .e2e.js 结尾的文件
        └── page2.js               // 页面 2，任意命名
    ├── global.css                 // 约定的全局样式文件，自动引入，也可以用 global.less
    ├── global.js                  // 可以在这里加入 polyfill
    ├── app.js                     // 运行时配置文件
├── .umirc.js                      // umi 配置，同 config/config.js，二选一
├── .env                           // 环境变量
└── package.json
```

### umi基本使用

```elm
npm init
npm install umi -D
```

**新建index页**

```elm
umi g page index
```

**启动服务器**

```elm
umi dev
```

#### 路由

##### **基础路由**

`umi`是支持约定式路由与配置式路由的，默认为约定式路由，约定式就是会根据`pages`目录自动生成路由配置。

假设`pages`目录结构如下：

```elm
+ pages/
  + users/
    - index.js
    - list.js
  - index.js
```

则umi会自动生成路由配置如下：

```js
[
  { path: '/', component: './pages/index.js' },
  { path: '/users/', component: './pages/users/index.js' },
  { path: '/users/list', component: './pages/users/list.js' },
]
```

> 若.umirc.(ts|js)或者config/config.(ts|js)文件对router进行了配置，约定式路由将改为配置式路由，新的页面将不会自动被umi编译

##### **动态路由**

umi里规定以`$`开头的文件或目录为动态路由

如以下目录结构：

```elm
+ pages/
  + $post/
    - index.js
    - comments.js
  + users/
    $id.js
  - index.js
```

会生成的路由配置如下：

```js
[
  { path: '/', component: './pages/index.js' },
  { path: '/users/:id', component: './pages/users/$id.js' },
  { path: '/:post/', component: './pages/$post/index.js' },
  { path: '/:post/comments', component: './pages/$post/comments.js' },
]
```

##### **可选的动态路由**

umi 里约定动态路由如果带 `$` 后缀，则为可选动态路由。

比如以下结构：

```elm
+ pages/
  + users/
    - $id$.js
  - index.js
```

生成的路由配置如下：

```js
[
  { path: '/': component: './pages/index.js' },
  { path: '/users/:id?': component: './pages/users/$id$.js' },
]
```

##### **嵌套路由**

umi 里约定目录下有 `_layout.js` 时会生成嵌套路由，以 `_layout.js` 为该目录的 layout 。

比如以下目录结构：

```elm
+ pages/
  + users/
    - _layout.js
    - $id.js
    - index.js
```

生成的路由配置如下：

```js
[
  { path: '/users', component: './pages/users/_layout.js',
    routes: [
     { path: '/users/', component: './pages/users/index.js' },
     { path: '/users/:id', component: './pages/users/$id.js' },
   ],
  },
]
```

##### **404 路由**

约定 `pages/404.js` 为 404 页面，需返回 React 组件。

比如

```react
export default () => {
  return (
    <div>I am a customized 404 page</div>
  );
};
```

> 注意：开发模式下，umi 会添加一个默认的 404 页面来辅助开发，但你仍然可通过精确地访问 `/404` 来验证 404 页面。

##### **通过注释扩展路由**

约定路由文件的首个注释如果包含 **yaml** 格式的配置，则会被用于扩展路由。

比如：

```elm
+ pages/
  - index.js
```

如果 `pages/index.js` 里包含：

```js
/**
 * title: Index Page
 * Routes:
 *   - ./src/routes/a.js
 *   - ./src/routes/b.js
 */
```

则会生成路由配置

```js
[
  { path: '/', component: './index.js',
    title: 'Index Page',
    Routes: [ './src/routes/a.js', './src/routes/b.js' ],
  },
]
```

##### **配置式路由**

如果你倾向于使用配置式的路由，可以配置 `.umirc.(ts|js)` 或者 `config/config.(ts|js)` [配置文件](https://umijs.org/zh/guide/config.html)中的 `routes` 属性，**此配置项存在时则不会对 `src/pages` 目录做约定式的解析**。

比如：

```js
export default {
  routes: [
    { path: '/', component: './a' },
    { path: '/list', component: './b', Routes: ['./routes/PrivateRoute.js'] },
    { path: '/users', component: './users/_layout',
      routes: [
        { path: '/users/detail', component: './users/detail' },
        { path: '/users/:id', component: './users/id' }
      ]
    },
  ],
};
```

注意：

1. component 是相对于 `src/pages` 目录的

##### **权限路由**

umi 的权限路由是通过配置路由的 `Routes` 属性来实现。约定式的通过 yaml 注释添加，配置式的直接配上即可。

比如有以下配置：

```js
[
  { path: '/', component: './pages/index.js' },
  { path: '/list', component: './pages/list.js', Routes: ['./routes/PrivateRoute.js'] },
]
```

然后 umi 会用 `./routes/PrivateRoute.js` 来渲染 `/list`。

`./routes/PrivateRoute.js` 文件示例：

```react
export default (props) => {
  return (
    <div>
      <div>PrivateRoute (routes/PrivateRoute.js)</div>
      { props.children }
    </div>
  );
}
```

#### antd引用

```elm
cnpm i antd -S
cnpm install umi-plugin-react -D
```

修改 config/config.js

```
// umi2的配置方式
export default {
  plugins: [
    // 有参数
    [
      'umi-plugin-react',
      {
        dva: {},
        antd: {},
      },
    ],
    './plugin',
  ],
};

// umi3的配置方式
export default {
  dva: {},
  antd: {}
};

// package.json
{
  "devDependencies": {
-   "umi": "^2"       // for umi2
+   "umi": "^3"       // for umi3
-   "umi-plugin-react": "^1"   // for umi2
+   "@umijs/preset-react": "^1"  // for umi3
  }
}
```

```react
import React from 'react';
import styles from './index.css';
import {Button} from 'antd'

export default function Page() {
  return (
    <div>
      <h1 className={styles.title}>Page index</h1>
      <Button>登录</Button>
    </div>
  );
}
```

#### umi3使用dva

```
https://blog.csdn.net/weixin_43787651/article/details/110224586

dva教程：
https://blog.csdn.net/qq_39523111/article/details/88050125?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242
```

## react整合electron

https://zhuanlan.zhihu.com/p/29164782



一、打包报 Application entry file “build\electron.js” in the “D:\workspace\electronDemo\demo-test\demo\dist\win-unpacked\resources\app.asar” does not exist

https://blog.csdn.net/qq_40593656/article/details/100101911

二、在react打包后静态资源在electron展示不了

https://segmentfault.com/a/1190000020229885?utm_source=tag-newest