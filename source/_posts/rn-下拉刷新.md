---
title: rn-下拉刷新
date: 2017-10-05 14:45:16
categories: 第三方插件使用
tags: [react-native]
---

PullToRefreshListView 提供了页面下拉刷新 上滑获取更多数据的功能

[GitHub 地址] (https://github.com/react-native-component/react-native-smart-pull-to-refresh-listview/)

其实提供了Android和iOS两种系统的刷新

<!--more-->

如果你的项目只服务其中一种系统 可以只引入其中一个

```javascript
import AndroidPullToRefreshListView from './PullToRefreshListView-android'
import IOSPullToRefreshListView from './PullToRefreshListView-ios'
```

#### 安装

```javascript
npm install react-native-smart-pull-to-refresh-listview --save
```

#### code

```html
 <PullToRefreshListView
  viewType={viewType}
  enableEmptySections={true}
  pagingEnabled={false}
  contentContainerStyle={styles.listView}
  dataSource={state.dataSource}
  renderRow={renderRow}
  renderHeader={this._renderHeader}
  renderFooter={this._renderFooter}
  onRefresh={this._onRefresh}
  onLoadMore={this._onLoadMore}
  autoLoadMore={true}
  pullUpDistance={1}
  pullUpStayDistance={35}
  pullDownDistance={pullOkMargin}
  pullDownStayDistance={defaultTopIndicatorHeight}
  scrollRenderAheadDistance={windowHeight}
  removeClippedSubviews={true}
  pageSize={20}
 />
```

#### 属性解析

1 viewType


2 dataSource

数据来源



3  renderRow

内置函数

```javascript
_renderRow = (rowData, sectionID, rowID) => {
      return (
          <View style={styles.thumbnail}>
              <View style={styles.textContainer}>
                  <Text>{rowData.text}</Text>
              </View>
          </View>
      )
  }
```

这个函数是组件封装好的 默认接受三个参数 第一个参数是渲染的数据

可以直接使用

```javascript
let renderRow = (rowData, _, index) => {
  return shouldHidden ? null : <ListRow {...props} {...rowData} />
}
```

4 renderheader

下拉刷新时候展示的头部loading组件部分

这个是一个函数  返回值是一个组件  此组件将作为loading组件展示在头部

而关于下拉刷新的几个状态值  比如未下拉刷新 正在下拉  松手状态  刷新完毕状态 都可以获取到

```javascript
_renderHeader = (viewState) => {
  let {pullState, pullDistancePercent} = viewState
  let {refresh_none, refresh_idle, will_refresh, refreshing} = PullToRefreshListView.constants.viewState
  pullDistancePercent = Math.round(pullDistancePercent * 100)
  switch(pullState) {
    case  refresh_none :
      return <Text> 请下拉用以刷新页面 </Text>
    ......
 }
}
```

可以看到几个状态值 全部保存在 PullToRefreshListView.constants.viewState 里面

5 renderFooter

同理 是在下拉获取更多的时候 在底部展示的组件

```javascript
_renderFooter = (viewState) => {
   let {pullState, pullDistancePercent} = viewState
   let {load_more_none, load_more_idle, will_load_more, loading_more, loaded_all, } = PullToRefreshListView.constants.viewState
   ......
}
```

则关于上拉的各种状态也是在PullToRefreshListView.constants.viewState中

6 autoLoadMore

是否开启上拉获取更多数据功能 默认值FALSE

7  关于处理下拉以及上滑的距离

 ```javascript
 pullUpDistance={1}
 pullUpStayDistance={35}
 pullDownDistance={pullOkMargin}
 pullDownStayDistance={defaultTopIndicatorHeight}
 ```

#### 方法

1 onRefresh

当下拉刷新的时候 触发此方法

2 onLoadMore

当上拉获取更多数据的时候 触发此方法


other

1 beginRefresh  相当于强制去执行一次下拉刷新

2 endRefresh 终止下拉刷新

3 endLoadMore 终止上拉获取更多数据



当开始执行下拉刷新 获取数据的过程  此时展示头部loading组件部分  当数据获取完毕 需要使用以上方法关闭loading状态

 ```javascript
 this._pullToRefreshListView.endRefresh()
// 或者
 this._pullToRefreshListView.endLoadMore(true)
 ```

如果不执行这个方法 加载状态会一直存在的
