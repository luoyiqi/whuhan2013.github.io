---
layout: post
title: React Native常用组件(二)
date: 2016-6-25
categories: blog
tags: [React Native]
description: React Native实例
---


**本文主要包括以下内容** 

1. listView实例            
2. Image组件实例
3. ProgressBarAndroid组件  
4. DrawerLayoutAndroid组件 
5. ScrollView组件

### listView实例 

主要讲述了从通过网络获取数据，并且加载到listView中的实例

**ListView的介绍**

Let's now modify this application to render all of this data in a ListView component, rather than just rendering the first movie.

Why is a ListView better than just rendering all of these elements or putting them in a ScrollView? Despite React being fast, rendering a possibly infinite list of elements could be slow. ListView schedules rendering of views so that you only display the ones on screen and those already rendered but off screen are removed from the native view hierarchy.


**简单例子**

```
'use strict';
import React, {
  AppRegistry,
  Component,
  StyleSheet,
  Text,
  View,
  ListView,
} from 'react-native';
var ListViewDemo = React.createClass({
    getInitialState: function() {
      var ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
        return {
          dataSource: ds.cloneWithRows(['row 1', 'row 2','row 3','row 4','row 5','row 6','row 7','row 8']),
        };
    },
    render: function() {
      return (
        <ListView
          dataSource={this.state.dataSource}
          renderRow={(rowData) => <Text>{rowData}</Text>}
        />
      );
    }
});
AppRegistry.registerComponent('ListViewDemo', () => ListViewDemo);
```


**复杂例子代码**

```
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 * @flow
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
   Navigator,
   ScrollView,
   TextInput,
   ListView,
  Text,
  Image,
    PixelRatio,
  View
} from 'react-native';

var REQUEST_URL = 'https://raw.githubusercontent.com/facebook/react-native/master/docs/MoviesExample.json';

var MOCKED_MOVIES_DATA = [
  {title: 'Title', year: '2015', posters: {thumbnail: 'http://i.imgur.com/UePbdph.jpg'}},
];

class Abc extends Component {
    constructor(props) {
    super(props);
     this.state = {
      dataSource: new ListView.DataSource({
        rowHasChanged: (row1, row2) => row1 !== row2,
      }),
      loaded: false,
    };
  }
 fetchData() {
    fetch(REQUEST_URL)
      .then((response) => response.json())
      .then((responseData) => {
        this.setState({
          dataSource: this.state.dataSource.cloneWithRows(responseData.movies),
          loaded: true,
        });
      })
      .done();
  }
  componentDidMount() {
    this.fetchData();
  }
  render() {
      if (!this.state.loaded) {
      return this.renderLoadingView();
    }

   return (
      <ListView
        dataSource={this.state.dataSource}
        renderRow={this.renderMovie}
        style={styles.listView}
      />
    );
       
    }



renderLoadingView() {
    return (
      <View style={styles.container}>
        <Text>
          Loading movies...
        </Text>
      </View>
    );
  }

  renderMovie(movie1) {
    return (
      <View style={styles.container}>
        <Image
          source={{uri: movie1.posters.thumbnail}}
          style={styles.thumbnail}
        />
        <View style={styles.rightContainer}>
          <Text style={styles.title}>{movie1.title}</Text>
          <Text style={styles.year}>{movie1.year}</Text>
        </View>
      </View>
    );
  }
}






const styles = StyleSheet.create({
 
   container: {
    flex: 1,
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  thumbnail: {
    width: 53,
    height: 81,
  },
   rightContainer: {
    flex: 1,
  },
  title: {
    fontSize: 20,
    marginBottom: 8,
    textAlign: 'center',
  },
  year: {
    textAlign: 'center',
  },
   listView: {
    paddingTop: 20,
    backgroundColor: '#F5FCFF',
  },
});

AppRegistry.registerComponent('Abc', () => Abc);
```


**效果如下** 

![](https://facebook.github.io/react-native/img/TutorialFinal.png)



### Image组件实例


**访问本地图片** 

```
<View  style={{marginLeft:10,marginTop:10}}>
      <Text style={{fontSize:16}}>'测试本地图片'</Text>
      <Image source={require('./img/my_icon.png')} />
 </View>
```

**加载使用APP中的图片** 

```
<Image source={{uri:'ic_launcher'}} style={{width: 40, height: 40}} />
```

不过如果要显示效果：希望大家做如下修改，因为现在android项目采用gradle，现在不会默认生成drawable文件夹中了，所以大家如果要演示效果，需要在res下面新建一个drawable文件夹，然后放一个图片进入，接着在重新打包运行即可(这边测试的是把ic_launcher.png图片放入到res/drawable文件夹中)。不过经测试drawable-hdpi这类的不同分辨率格式文件夹也可以运行。

该适用于调试版本，如果采用发布版本就需要例如'image!xx.png'格式的访问方式了


**加载网络图片**   

```
<Image source={{uri:'http://mta.zttit.com:8080/images/ZTT_1404756641470_image.jpg'}}  style={{width:100,height:100}}/>
```

**Image实现某些控件的背景图效果**

React Native中支持嵌套的方式，例如我们现在有一个Text组件，假如要实现背景图的效果，那么可以使用Image嵌套的Text的方式，然后Image加载图片方式实现,例如代码如下:

```
<Image source={require('./img/my_icon.png')} >
           <Text style={{color:'red'}}>下面是背景图</Text>
</Image>
```

**Image实例仿美团头部**

```
/**
 * 模仿美团首页顶部分类效果
 * Sample React Native App
 * https://github.com/facebook/react-native
 */
'use strict';
import React, {
  AppRegistry,
  Component,
  StyleSheet,
  Text,
  View,
  Image,
} from 'react-native';
class TestImage extends Component {
  render() {
    return (
      <View style={{marginLeft:5,marginTop:10,marginRight:5}}>
 
         <View style={{flexDirection:'row'}}>
             <View style={{width:70}}>
                 <Image source={require('./img/one.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,textAlign:'center',fontSize:11,color:'#555555'}}>美食</Text>
             </View>
              <View style={{width:70}}>
                 <Image source={require('./img/two.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>电影</Text>
             </View>
             <View style={{width:70}}>
                 <Image source={require('./img/three.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>酒店</Text>
             </View>
             <View style={{width:70}}>
                 <Image source={require('./img/four.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>KTV</Text>
             </View>
             <View style={{width:70}}>
                 <Image source={require('./img/five.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>外卖</Text>
             </View>
          </View>
          <View style={{flexDirection:'row',marginTop:10}}>
             <View style={{width:70}}>
                 <Image source={require('./img/six.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,textAlign:'center',fontSize:11,color:'#555555'}}>优惠买单</Text>
             </View>
              <View style={{width:70}}>
                 <Image source={require('./img/seven.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>周边游</Text>
             </View>
             <View style={{width:70}}>
                 <Image source={require('./img/eight.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>休闲娱乐</Text>
             </View>
             <View style={{width:70}}>
                 <Image source={require('./img/nine.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>今日新单</Text>
             </View>
             <View style={{width:70}}>
                 <Image source={require('./img/ten.png')} style={{alignSelf:'center',width:45,height:45}} />
                 <Text style={{marginTop:5,alignSelf:'center',fontSize:11,color:'#555555',textAlign:'center'}}>丽人</Text>
             </View>
          </View>
      </View>
    );
  }
}

AppRegistry.registerComponent('TestImage', () => TestImage);
```



![](http://lookcode-wordpress.stor.sinaapp.com/uploads/2016/01/64.jpg)


### ProgressBarAndroid组件

**属性方法** 

支持View控件的属性方法 (这些属性是从View控件中继承下来)  例如:大小,布局,边距啊

- color  设置进度的颜色属性值
- indeterminate 设置是否要显示一个默认的进度信息，该如果styleAttr的风格设置成Horizontal的时候该值必须设置成false
- progress  number  设置当前的加载进度值(该值在0-1之间)
- styleAttr    进度条框的风格 ，可以取的值如下:
- Horizontal
 +Small
 +Large
 +Inverse
 +SmallInverse
 +LargeInverse



**ProgressBarAndroid使用实例**  

```
'use strict';
import React, {
  AppRegistry,
  Component,
  StyleSheet,
  Text,
  View,
  ProgressBarAndroid,
} from 'react-native';
 
class ProgressBarDemo extends Component {
  render() {
    return (
      <View >
        <Text>
            ProgressBarAndroid控件实例
        </Text>
        <ProgressBarAndroid  color="red" styleAttr='Inverse'/>
        <ProgressBarAndroid  color="green" styleAttr='Horizontal' progress={0.2} 
            indeterminate={false} style={{marginTop:10}}/>
        <ProgressBarAndroid  color="green" styleAttr='Horizontal'
            indeterminate={true} style={{marginTop:10}}/>
        <ProgressBarAndroid  color="black" styleAttr='SmallInverse'
            style={{marginTop:10}}/>
        <ProgressBarAndroid  styleAttr='LargeInverse'
            style={{marginTop:10}}/>
      </View>
    );
  }
}
AppRegistry.registerComponent('ProgressBarDemo', () => ProgressBarDemo);
```

**效果如下** 

![](http://lookcode-wordpress.stor.sinaapp.com/uploads/2016/01/212.jpg)

### DrawerLayoutAndroid组件

具体基本使用实例代码如下:


```
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 */
'use strict';
import React, {
  AppRegistry,
  Component,
  StyleSheet,
  Text,
  View,
  DrawerLayoutAndroid,
} from 'react-native';
 
class DrawerLayoutDemo extends Component {
 statics: {
    title: '<ScrollView>',
    description: 'Component that enables scrolling through child components.'
  },
 makeItems: function(nItems: number, styles): Array<any> {
    var items = [];
    for (var i = 0; i < nItems; i++) {
       items[i] = (
         <TouchableOpacity key={i} style={styles}>
           <Text>{'Item ' + i}</Text>
         </TouchableOpacity>
       );
    }
    return items;
  },
  render() {
   // One of the items is a horizontal scroll view
    var items = this.makeItems(NUM_ITEMS, styles.itemWrapper);
    items[4] = (
      <ScrollView key={'scrollView'} horizontal={true}>
        {this.makeItems(NUM_ITEMS, [styles.itemWrapper, styles.horizontalItemWrapper])}
      </ScrollView>
    );
 
    var verticalScrollView = (
      <ScrollView style={styles.verticalScrollView}>
        {items}
      </ScrollView>
    );
 
    return verticalScrollView;
  }
}
const styles = StyleSheet.create({
});
AppRegistry.registerComponent('DrawerLayoutDemo', () => DrawerLayoutDemo);
```


**效果如下** 

![](http://lookcode-wordpress.stor.sinaapp.com/uploads/2016/01/213.jpg)



### ScrollView组件 


```
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 * @flow
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Image,
  Text,
  TextInput,
   ScrollView,
    TouchableOpacity,
  DrawerLayoutAndroid,
   ProgressBarAndroid,
  View
} from 'react-native';
var NUM_ITEMS = 20;

class MyProject extends Component {
   statics: {
    title: '<ScrollView>',
    description: 'Component that enables scrolling through child components.'
  }

  makeItems(nItems: number, styles): Array<any> {
    var items = [];
    for (var i = 0; i < nItems; i++) {
       items[i] = (
         <TouchableOpacity key={i} style={styles}>
           <Text>{'Item ' + i}</Text>
         </TouchableOpacity>
       );
    }
    return items;
  }
 
  render() {
    // One of the items is a horizontal scroll view
    var items = this.makeItems(NUM_ITEMS, styles.itemWrapper);
    items[4] = (
      <ScrollView key={'scrollView'} horizontal={true}>
        {this.makeItems(NUM_ITEMS, [styles.itemWrapper, styles.horizontalItemWrapper])}
      </ScrollView>
    );
 
    var verticalScrollView = (
      <ScrollView style={styles.verticalScrollView}>
        {items}
      </ScrollView>
    );
 
    return verticalScrollView;
  }
}

const styles = StyleSheet.create({
  
   verticalScrollView: {
    margin: 10,
  },
  itemWrapper: {
    backgroundColor: '#dddddd',
    alignItems: 'center',
    borderRadius: 5,
    borderWidth: 5,
    borderColor: '#a52a2a',
    padding: 30,
    margin: 5,
  },
  horizontalItemWrapper: {
    padding: 50
  }
});

AppRegistry.registerComponent('MyProject', () => MyProject);
```

**效果如下**


![](https://raw.githubusercontent.com/whuhan2013/ImageRepertory/master/react1.png)