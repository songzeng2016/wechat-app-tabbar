# wechat-app-tabbar
## 微信小程序 自定义tabbar

[项目地址，欢迎 star](https://github.com/songzeng2016/wechat-app-tabbar) 

[https://github.com/songzeng2016/wechat-app-tabbar](https://github.com/songzeng2016/wechat-app-tabbar)

### 前言

项目中我们可能会用到两层 tabbar 而小程序只能配置出一层，或者我们想自定义 tabbar 的样式或功能，这时候就需要我们自己定义 tabbar。
先来张图看下效果，下面是实现步骤。

![tabbar](https://github.com/songzeng2016/wechat-app-tabbar/raw/master/images/GIF.gif)   

### 创建wxml模板

```html
<template name="tabbar">
    <view class="tabbar_box" style="background-color:{{tabbar.backgroundColor}}; border-top-color:{{tabbar.borderStyle}}; {{tabbar.position == 'top' ? 'top:0' : 'bottom:0'}}">
        <block wx:for="{{tabbar.list}}" wx:for-item="item" wx:key="index">
            <navigator class="tabbar_nav" url="{{item.pagePath}}" style="width:{{1/tabbar.list.length*100}}%; color:{{item.selected ? tabbar.selectedColor : tabbar.color}}" open-type="redirect">
                <image class="tabbar_icon" src="{{item.selected ? item.selectedIconPath : item.iconPath}}"></image>
                <text>{{item.text}}</text>
            </navigator>
        </block>
    </view>
</template>
``` 
### wxss布局

```css
.tabbar_box{
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    position: fixed;
    bottom: 0;
    left: 0;
    z-index: 999;
    width: 100%;
    height: 120rpx;
    border-top: 1rpx solid gray; 
}

.tabbar_nav{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    font-size: 28rpx;
    height: 100%;
}

.tabbar_icon{
    width: 61rpx;
    height: 61rpx;
}
```
    布局不是重点也可以自定义布局也可以引用我写好的样式

### 重点来了 tabbar的参数配置

```javascript
tabbar:{
      color: "#000000",
      selectedColor: "#0f87ff",
      backgroundColor: "#ffffff",
      borderStyle: "black",
      list: [
        {
          pagePath: "/pages/tabbar/tabbar",
          text: "项目",
          iconPath: "/images/item.png",
          selectedIconPath: "/images/item_HL.png",
          selected: true
        },
        {
          pagePath: "/pages/address/address",
          text: "通讯录",
          iconPath: "/images/ts.png",
          selectedIconPath: "/images/ts1.png",
          selected: false
        },
        {
          pagePath: "/pages/personal/personal",
          text: "文件",
          iconPath: "/images/wjj.png",
          selectedIconPath: "/images/wjj1.png",
          selected: false
        }
      ],
      position: "bottom"
    }
    
```
    有没有感觉很熟悉，没错就是你熟悉的tababar配置，仅仅增加了一个selected参数来表示选中的状态
    另外一点要注意的是我们的tabbar数据配置在app.js里面而不是app.json里面
    
### 最后还有一个比较重要的点 在app.js里面的一个函数
```javascript
editTabBar: function(){
    var tabbar = this.globalData.tabbar,
        currentPages = getCurrentPages(),
        _this = currentPages[currentPages.length - 1],
        pagePath = _this.__route__;
    (pagePath.indexOf('/') != 0) && (pagePath = '/' + pagePath);
    for(var i in tabbar.list){
      tabbar.list[i].selected = false;
      (tabbar.list[i].pagePath == pagePath) && (tabbar.list[i].selected = true);
    }
    _this.setData({
      tabbar: tabbar
    });
  },
```
### 我们完整的app.js是这样的
```javascript
//app.js
App({
  onLaunch: function () {
    //调用API从本地缓存中获取数据
    var logs = wx.getStorageSync('logs') || []
    logs.unshift(Date.now())
    wx.setStorageSync('logs', logs)
  },
  getUserInfo:function(cb){
    var that = this
    if(this.globalData.userInfo){
      typeof cb == "function" && cb(this.globalData.userInfo)
    }else{
      //调用登录接口
      wx.login({
        success: function () {
          wx.getUserInfo({
            success: function (res) {
              that.globalData.userInfo = res.userInfo
              typeof cb == "function" && cb(that.globalData.userInfo)
            }
          })
        }
      })
    }
  },
  editTabBar: function(){
    var tabbar = this.globalData.tabbar,
        currentPages = getCurrentPages(),
        _this = currentPages[currentPages.length - 1],
        pagePath = _this.__route__;
    (pagePath.indexOf('/') != 0) && (pagePath = '/' + pagePath);
    for(var i in tabbar.list){
      tabbar.list[i].selected = false;
      (tabbar.list[i].pagePath == pagePath) && (tabbar.list[i].selected = true);
    }
    _this.setData({
      tabbar: tabbar
    });
  },
  globalData:{
    userInfo:null,
    tabbar:{
      color: "#000000",
      selectedColor: "#0f87ff",
      backgroundColor: "#ffffff",
      borderStyle: "black",
      list: [
        {
          pagePath: "/pages/tabbar/tabbar",
          text: "项目",
          iconPath: "/images/item.png",
          selectedIconPath: "/images/item_HL.png",
          selected: true
        },
        {
          pagePath: "/pages/address/address",
          text: "通讯录",
          iconPath: "/images/ts.png",
          selectedIconPath: "/images/ts1.png",
          selected: false
        },
        {
          pagePath: "/pages/personal/personal",
          text: "文件",
          iconPath: "/images/wjj.png",
          selectedIconPath: "/images/wjj1.png",
          selected: false
        }
      ],
      position: "bottom"
    }
  }
})
```
    生成的东西我没有删掉
    
### 到这准备工作已经完成  下面就是怎么使用

#### 在wxml引入创建的模板并使用
```html
<import src="../tabbar/tabbar.wxml"/>
<template is="tabbar" data="{{tabbar}}"/>

```
    我这里是相对路径，最好写成绝对路径
    
#### wxss中引入样式
```css
@import "/pages/tabbar/tabbar.wxss"

```

#### js中调用函数

```javascript
//获取app实例
var app = getApp();

Page({
  data:{
    tabbar:{}
  },
  onLoad:function(options){
    // 页面初始化 options为页面跳转所带来的参数
    
    //调用函数
    app.editTabBar(); 
  },
  onReady:function(){
    // 页面渲染完成
  },
  onShow:function(){
    // 页面显示
  },
  onHide:function(){
    // 页面隐藏
  },
  onUnload:function(){
    // 页面关闭
  }
})
```

    注意在每个配置在tabbar中的页面都要有这三步，因为这个是页面跳转了
    还有一个问题就是页面跳转的时候会闪一下，网络慢的时候更明显
    后面我会做一个不是跳转页面的tabbar
    
不跳转页面的暂时还没有更好的思路先给大家推荐一个  [不跳转页面的tabbar](https://github.com/marlti7/wx-mina-custom-tabbar)

[微信小程序仿微信， QQ 向左滑动删除操作](https://github.com/songzeng2016/wechat-app-LeftSlide)

[微信小程序 自定义tabbar](https://github.com/songzeng2016/wechat-app-tabbar)
