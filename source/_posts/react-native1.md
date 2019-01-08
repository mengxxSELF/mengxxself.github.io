---
title: react-native1
date: 2017-10-04 12:59:07
categories: javascript
tags: react-native
---

<!-- description: RN的学习 -->

本文搭建的是Mac环境的RN

### 环境搭建

[官网链接](http://reactnative.cn/docs/0.45/getting-started.html)

安装所需模块按照官网中逐步安装即可

<!--more-->

所有模块安装完毕 开始创建项目

```javascript
react-native init HelloWorld
```

这样就可以创建一个完成的RN项目

启动项目 在命令行中输入

```javascript
react-native run-ios
```

即可启动此项目


#### 项目启动中bug

在尝试启动项目中 一直报错

```javascript
‘boost/iterator/iterator_adaptor.hpp’ file not found
```

产生原因：

```javascript
* /Users/xxx/.rncache 中 boost_1_63_0.tar.gz， double-conversion-1.1.5.tar.gz， folly-2016.09.26.00.tar.gz， glog-0.3.4.tar.gz 等文件下载不完整
```

** 解决方案 ** 删除 .rncache 后重新下载，或手动下载后放入 .rncache 中

可能是由于网络问题或者无法翻墙 导致此文件下载不完整

然后一旦此文件存在 后面的发现有这个 就不再去安装它了 所以你会发现  即使你把所有项目删掉重新来 也依旧显示这个错误


#### 相关文章导读

1 [《RN 开发错误警告处理》](http://reactnative.cn/docs/0.45/getting-started.html)

2 [RN 课程自学](https://github.com/vczero/react-native-lesson)


### 基础组件

RN 的元素渲染与React不同  前者就是使用的普通HTML标签 而RN则是新的一套语义化更加明白的标签

#### Loading

 ```html
 <ActivityIndicator
 animating={this.state.animating}
 style={[styles.centering, {height: 80}]}
 color='#000'
 size='large'
 />
 ```

其属性animating 表示是否显示loading图片  默认TRUE 显示
color  loading的颜色
size  大小设置  属性值 可写 small  large 不可以为数字或者其他


#### Button

```html
<Button
onPress={this.onPressLearnMore}
title='click the button'
color='#841584'
/>
```

属性 onPress 当点击的时候 执行此函数

其余属性

disabled  为TRUE 时候 则按钮不可点击
color 按钮中字体颜色


#### Alert  弹出层

```html
import {Alert} from 'react-native'
---
<Button onPress={() => Alert.alert('you clicked the button') }>
```

#### Image

```html
<Image
   style={styles.logo}
   source={{uri: 'http://facebook.github.io/react/img/logo_og.png'}}
/>
```

注意这里的属性source  后面的URL  如果是http开头的 很可能在iOS上打不开  推荐使用https开头

#### listView

展示数据列表

```javascript
constructor(props) {
  super(props);
  var ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
  this.state = {
    dataSource: ds.cloneWithRows(['row 1', 'row 2']),
  };
}
render() {
  return (
    <ListView
      dataSource={this.state.dataSource}
      renderRow={(rowData) => <Text>{rowData}</Text>}
    />
  );
}
```

最基本的使用方式就是创建一个ListView.DataSource数据源，然后给它传递一个普通的数据数组，

再使用数据源来实例化一个ListView组件，并且定义它的renderRow回调函数

这个函数会接受数组中的每个数据作为参数，去渲染每一行，返回一个可渲染的组件（作为listview的每一行）

属性参数解析

#####  dataSource

列表依赖的数据源

##### initialListSize 【number】

指定在组件刚挂载的时候渲染多少行数据

用这个属性来确保首屏显示合适数量的数据，而不是花费太多帧逐步显示出来

##### onChangeVisibleRows [function]

```javascript
(visibleRows, changedRows) => void
```

视野内可见的行的集合变化的时候调用此回调函数

visibleRows 以 { sectionID: { rowID: true }}的格式包含了所有可见行，而changedRows 以{ sectionID: { rowID: true | false }}的格式包含了所有刚刚改变了可见性的行

其中如果值为true表示一个行变得可见，而为false表示行刚刚离开可视区域而变得不可见。

##### pageSize 【number】

每次事件循环（每帧）渲染的行数

##### renderFooter 【function】

##### renderHeader 【function】

```javascript
() => renderable
```

页头与页脚会在每次渲染过程中都重新渲染【如果提供了这些属性】

如果它们重绘的性能开销很大，把他们包装到一个StaticContainer或者其它恰当的结构中。页脚会永远在列表的最底部，而页头会在最顶部

##### renderRow 【function】

(rowData, sectionID, rowID, highlightRow) => renderable

从数据源(Data source)中接受一条数据，以及它和它所在section的ID。返回一个可渲染的组件来为这行数据进行渲染。默认情况下参数中的数据就是放进数据源中的数据本身，不过也可以提供一些转换器。

如果某一行正在被高亮（通过调用highlightRow函数），ListView会得到相应的通知。当一行被高亮时，其两侧的分割线会被隐藏。行的高亮状态可以通过调用highlightRow(null)来重置



#### ListView.DataSource

要更新datasource中的数据，每次都重新调用cloneWithRows方法

数据源中的数据本身是不可修改的，所以请勿直接尝试修改

clone方法会自动提取新数据并进行逐行对比（使用rowHasChanged方法中的策略）这样ListView就知道哪些行需要重新渲染了

##### cloneWithRows(dataBlob, rowIdentities)

根据指定的dataBlob和 rowIdentities为ListViewDataSource复制填充数据。dataBlob即原始数据。需要在初始化时定义抽取函数（否则使用默认的抽取函数）。

rowIdentities是一个二维数组，包含了行数据对应的id标识符，例如[['a1', 'a2'], ['b1', 'b2', 'b3'], ...]。如果没有指定此数组，则默认取行数据的key。

注：此方法实际并没有复制数据。它只是重新创建一个datasource，然后将你指定的dataBlob传递给构造函数中指定的提取函数，因而会抛弃先前的数据。如果你希望保留先前的数据，则必须先自行进行新老数据的合并处理，然后再将合并后的结果作为dataBlob传递给此方法调用。





####  ScrollView  视图滚动

属性解析

##### horizontal

当此属性为true的时候，所有的子视图会在水平方向上排成一行，而不是默认的在垂直方向上排成一列

默认值为false

##### showsHorizontalScrollIndicator bool

当此属性为true的时候，显示一个水平方向的滚动条

##### scrollEventThrottle   【number】

这个属性控制在滚动过程中，scroll事件被调用的频率（单位是每秒事件数量）

更大的数值能够更及时的跟踪滚动位置，不过可能会带来性能问题

默认值为0，意味着每次视图被滚动，scroll事件只会被调用一次

##### onScroll

滚动是触发的方法

##### scrollEnabled bool

当值为false的时候，内容不能滚动，默认值为true


#### TouchableOpacity

本组件用于封装视图，使其可以正确响应触摸操作。当按下的时候，封装的视图的不透明度会降低。这个过程并不会真正改变视图层级

使用案例

```html
<TouchableOpacity onPress={this._onPressButton}>
   <Image
     style={styles.button}
     source={require('image!myButton')}
   />
</TouchableOpacity>
```

### 项目调试

当你的项目在模拟器中运行起来之后 需要对项目进行调试

** 针对于iOS开发 **

#### Developer Menu

在模拟器中开启 Developer Menu  快捷键 com + D

#### Chrome Developer Tools

谷歌开发工具是大家非常熟悉的了

第一步：启动远程调试

Developer Menu下单击”Debug JS Remotely” 启动JS远程调试功能。此时Chrome会被打开

第二步：打开Chrome开发者工具

在该“http://localhost:8081/debugger-ui.”Tab页下打开开发者工具。打开Chrome菜单->选择更多工具->选择开发者工具

然后选择source 进行调试


#### other

1 在调试模拟器的时候发现速度很慢  估计是在刷新页面的时候 误点击了commond + T 导致开启了slowe animation
