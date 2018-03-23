---
title: 基于JQuery网页漂浮广告窗口
date: 2017-08-29 10:21:11
tags: 转载
categories: JavaScript
---

### 首先说一下思路

1.漂浮的窗口肯定是`position:absolute`脱离文档流的

2.在浏览器可视范围内飘动

3.飘动过程可分解为垂直（上、下）和水平（左、右）方向，同一时间内会同时向垂直和水平两个方向同时移动，可能的情况会有上左、上右、下左、下右

基本的思路是创建一个函数，函数的作用是使元素移动，然后定时执行这个函数，以让元素持续移动，形成漂浮效果。基本上这些持续的元素移动都是基于 `setInterval(func,delay)` 函数，例如：`setInterval(move,30)`，第一个参数是要定时执行的函数，第二个参数是时间间隔，单位是毫秒。如果不懂setInterval的请见：[setInterval  介绍](http://www.w3school.com.cn/jsref/met_win_setinterval.asp)

首先这是基于JQuery写的，调用都是一些基础的接口，应该任何版本的JQuery都可以，我用的是1.9的。

<!--more-->

### 效果图，箭头是大概的运行轨迹

![](http://img.blog.csdn.net/20160406094510715?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 代码

浮窗Css样式
```css
<style>
        .floadAd { position: absolute;z-index: 999900; display: none; }
        .floadAd .item { display: block; }
        .floadAd .item img { vertical-align: bottom; }/* a img 的组合浏览器默认下边会有几个像素的空白，这里可以消除空白*/
</style>
```
Html 标记，里面的内容是可以按自己的需要来写的

```html
<div id="floadAD" class="floadAd">
    <a class="close" href="javascript:void();" style="color: red">×关闭</a>
    <a class="item" title='首届党建文化节' href="http://unsun.net" target="_blank">
        <img src="App_UpLoad/image/20160405/20160405172215_2907.png" alt="首届党建文化节" />
    </a>
</div>
```
Js代码，我没有把它封装到JQuery的插件里面，有需要可以自己去封装

```javascript
FloatAd("#floadAD");//调用

//广告漂浮窗口
function FloatAd(selector) {
    var obj = $(selector);
    if (obj.find(".item").length == 0) return;//如果没有内容，不执行
    var windowHeight = $(window).height();//浏览器高度
    var windowWidth = $(window).width();//浏览器宽度
    var dirX = -1.5;//每次水平漂浮方向及距离(单位：px)，正数向右，负数向左，如果越大的话就会看起来越不流畅，但在某些需求下你可能会需要这种效果
    var dirY = -1;//每次垂直漂浮方向及距离(单位：px)，正数向下，负数向上，如果越大的话就会看起来越不流畅，但在某些需求下你可能会需要这种效果

    var delay = 30;//定期执行的时间间隔，单位毫秒
    obj.css({ left: windowWidth / 2 - obj.width() / 2 + "px", top: windowHeight / 2 - obj.height() / 2 + "px" });//把元素设置成在页面中间
    obj.show();//元素默认是隐藏的，避免上一句代码改变位置视觉突兀，改变位置后再显示出来
    var handler = setInterval(move, delay);//定期执行，返回一个值，这个值可以用来取消定期执行

    obj.hover(function() {//鼠标经过时暂停，离开时继续
        clearInterval(handler);//取消定期执行
    }, function() {
        handler = setInterval(move, delay);
    });

    obj.find(".close").click(function() {//绑定关闭按钮事件
        close();
    });
    $(window).resize(function() {//当改变窗口大小时，重新获取浏览器大小，以保证不会过界（飘出浏览器可视范围）或漂的范围小于新的大小
        windowHeight = $(window).height();//浏览器高度
        windowWidth = $(window).width();//浏览器宽度
    });
    function move() {//定期执行的函数，使元素移动
        var currentPos = obj.position();//获取当前位置，这是JQuery的函数，具体见：http://hemin.cn/jq/position.html
        var nextPosX = currentPos.left + dirX;//下一个水平位置
        var nextPosY = currentPos.top + dirY;//下一个垂直位置

        if (nextPosX >= windowWidth - obj.width()) {//这一段是本站特有的需求，当漂浮到右边时关闭漂浮窗口，如不需要可删除
            close();
        }

        if (nextPosX <= 0 || nextPosX >= windowWidth - obj.width()) {//如果达到左边，或者达到右边，则改变为相反方向
            dirX = dirX * -1;//改变方向
            nextPosX = currentPos.left + dirX;//为了不过界，重新获取下一个位置
        }
        if (nextPosY <= 0 || nextPosY >= windowHeight - obj.height() - 5) {//如果达到上边，或者达到下边，则改变为相反方向。            
            dirY = dirY * -1;//改变方向
            nextPosY = currentPos.top + dirY;//为了不过界，重新获取下一个位置
        }
        obj.css({ left: nextPosX + "px", top: nextPosY + "px" });//移动到下一个位置
    }

    function close() {//停止漂浮，并销毁漂浮窗口
        clearInterval(handler);
        obj.remove();
    }
}
```

我已经把注释写的很清楚，这里就不再做多解释了，如果有有问题欢迎提出交流，一起进步。

第一次写博客，写的不好欢迎提出来让我改进![大笑](http://static.blog.csdn.net/xheditor/xheditor_emot/default/laugh.gif)

### 个人观点
之前的博主把内容写得很清楚，我就不做多余的说明，我只想说一句：**666**。
原文链接:[基于JQuery网页漂浮广告窗口Js详解](http://blog.csdn.net/u011427035/article/details/51072116)