---
title: react状态管理
date: 2019-01-16 14:12:37
tags: [react, redux]
---

![](https://user-gold-cdn.xitu.io/2019/1/15/168508594a753ef8?w=847&h=193&f=png&s=40010)

> redux saga dva 科普篇

<!-- More -->

### redux

最基础的就是使用 redux 来处理

这里是展示组件结构

```js
import { connect } from 'react-redux'
import { valueAdd, valueReduce } from './redux/action'

class Main extends Component {
  render () {
    return (
      <div className="App">
        <header className="App-header">
          <p onClick={() => this.props.valueAdd()} > 点击 + 1 </p>
          <p onClick={() => this.props.valueReduce()} > 点击 - 1 </p>
          <div> i am {this.props.number} </div>
        </header>
      </div>
    )
  }
const MainBox = connect(
  (state) => { return { number: state.number }},
  dispatch => {
    return {
      addNumber: () => { dispatch(valueAdd()) },
      reduceNumber: () => { dispatch(valueReduce()) }
    }
  }
)(Main)
export default MainBox
```

借助 bindActionCreators 调整一下

```js
import { connect } from 'react-redux'
import { bindActionCreators } from 'redux'
import * as action from './redux/action'

@connect(
  ({number}) => ({number}),
  dispatch => bindActionCreators(action, dispatch)
)
class Main extends Component {
  render () {
    return (
      <div className="App">
        <header className="App-header">
          <p onClick={() => this.props.valueAdd()} > 点击 + 1 </p>
          <p onClick={() => this.props.valueReduce()} > 点击 - 1 </p>
          <div> i am {this.props.number} </div>
        </header>
      </div>
    )
  }
}
export default Main
```

### redux-saga

[文档阅读](https://redux-saga-in-chinese.js.org/)

#### 常用API

| 方法 | 建议 |
| --------------- | ---------------  |
| takeEvery | 监听派发的动作，如果action type匹配的话，会执行对应的函数  |
| put | 派发Action， 可以理解成为redux中的dispatch函数 |
| call | 调用函数执行，阻塞 |
| fork | 调用函数执行，不会阻塞 |

#### 使用方式

对比于 redux，使用saga的时候只需要调整一下store的生成

```js

import { applyMiddleware, createStore } from 'redux';
import createSagaMiddleware from "redux-saga";
import rootSaga from "./saga" //引入rootSaga
import reducer from './reducer'
let sagaMiddleware = createSagaMiddleware()

export const store = createStore(
  reducer,
  applyMiddleware(sagaMiddleware),
);
sagaMiddleware.run(rootSaga); // 开始执行rootSaga
```
添加一个 saga 文件

```js
function * add () {
  // 派发Action
  yield put({ type: ACTION_ADD })
}

function * reduce () {
  yield put({type: ACTION_REDUCER})
}

function * watchAdd() {
 // 监听派发给仓库的动作，如果动作类型匹配的话，会执行对应的监听生成器
  yield takeEvery(ACTION_ADD_SAGA, add)
  yield takeEvery(ACTION_REDUCER_SAGA, reduce)
}

export default function * rootSaga() {
  yield watchAdd()
}
```
saga 有三种，做工作的 进行监听的 以及 rootsage

1 worker saga 做具体的工作，如调用API，进行异步请求，获取异步封装结果

2 watcher saga 监听被dispatch的actions,当接受到action或者知道其被触发时，调用worker执行任务

3 root saga 启动saga的唯一入口

### dva

dva 可以视为内部封装了redux-saga 

安装

```js
npm install -g dva-cli
```

使用dva生成项目

```
dva new xxxname
```

dva 项目结构

![dva](https://user-gold-cdn.xitu.io/2019/1/15/168503760db0ff26?w=338&h=332&f=png&s=24953)

dva 的文件结构命名有点奇怪， 比如这个 routes 文件夹 其实不是路由处理，里面是组件，（与该路由对应的组件）

models 则是将 action  reducer action_type 编写到一起了

#### 入口文件 index.js

在index.js 实现一些配置 比如 定义默认state 添加中间件 引入model=

```js
import dva from 'dva';
import {createLogger} from 'redux-logger';
// 1. Initialize 
const app = dva({});

// 2. Plugins
// app.use({});

// 3. Model
app.model(require('./models/main').default);
app.model(require('./models/users').default);

// 4. Router
app.router(require('./router').default);

// 5. Start
app.start('#root');
```

可以设置的全部属性参数为 

```js
const app = dva({
  history, // 路由控制 
  initialState, // 基础state 优先级高于 model 中的 state
  onError, // effect 执行错误或 subscription 通过 done 主动抛错时触发，可用于管理全局出错状态
  onAction, // 在action被dispatch时触发，用于注册 redux 中间件。支持函数或函数数组格式
  onStateChange, // 顾名思义，在state变动的时候触发
  onReducer,
  onEffect,
  onHmr,
  extraReducers,
  extraEnhancers,
})
```

* app.use

可以添加中间件

* app.model

所有的model都要通过这个方法挂载到app

* app.router

挂载定义的路由

* app.start

启动app

#### 核心部分 models

```js
export defult {
  namespace: '',
  state: '',
  reducers: {},
  effects: {},
  subscriptions: {}
}
```

| key | 含义 |
| --------| ---------- |
| namespace | model 的命名空间，是在全局 state 上的属性  |
| state | state 初始值 |
| reducers | 唯一可以修改 state 的地方，由 action 触发 |
| effects | 用于处理异步操作和业务逻辑，不直接修改 state。由action 触发，|
| subscriptions | 用于订阅一个数据源，然后根据需要 dispatch 相应的 action |

感觉如果项目决定使用 redux-saga 可以考虑直接使用 dva 


![流程图](https://user-gold-cdn.xitu.io/2019/1/15/168508cae7c1ea33?w=845&h=468&f=png&s=45164)

### umi 呜咪

![mimi](https://user-gold-cdn.xitu.io/2019/1/15/168505a4c26e0964?w=560&h=358&f=png&s=355038)

（和喵喵并没有什么关系）

```js
npm i -g umi
```
创建文件

```js
mkdir project && cd project && yarn create umi && cnpm i 
```

这个没什么好说的，就一个项目创建脚手架，可以包含测试 编译 部署等流程 就像使用种组装了一辆汽车 而用户来选择需不需要加车窗 轮子等部件


### 参考文章

* [dav 基本使用](https://www.jianshu.com/p/21f8ed30e761)

