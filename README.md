# wechat-app-tabbar
##微信小程序 自定义tabbar

![tabbar](https://github.com/songzeng2016/wechat-app-tabbar/raw/master/images/GIF.gif)   

###创建wxml模板

```
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
###wxss布局

```
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

##重点来了 tabbar的参数配置

```
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
    
