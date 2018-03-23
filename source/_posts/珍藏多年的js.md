---
title: 珍藏多年的JS
date: 2017-08-29 17:21:11
tags: 原创
categories: JavaScript
---

**这些都是工作中用到的比较常用或者有意思的js函数，持续更新中...**
<!--more-->

### 截取浏览器地址栏中的url
#### 方法一
```JavaScript
function getUrlVars(){ 
　　var vars = [], hash; 
　　var hashes = window.location.href.slice(window.location.href.indexOf('?')+1).split('&'); 
　　for(var i = 0; i < hashes.length; i++) { 
　　　　hash = hashes[i].split('='); 
　　　　vars.push(hash[0]); 
　　　　vars[hash[0]] = hash[1]; 
　　} 
    return vars; 
} 
var params  = getUrlVars();
var cateId  = params[params[0]]; //0是第一个
var cateId  = parseInt(cateId);  //字符串转数字
var type    = typeof(cateId);    //查看数据类型
console.log(cateId);
console.log(type);
```
#### 方法二
```JavaScript
var str= window.location.href;
var cateId = str.slice(-11);  
console.log(cateId);
```
#### 方法三
```JavaScript
function GetQueryString(name){
    var reg = new RegExp("(^|&)"+ name +"=([^&]*)(&|$)");
    var r = window.location.search.substr(1).match(reg);
    if(r!=null)return  unescape(r[2]); return null;
}
var cateId= GetQueryString("cateId");
console.log(cateId);
```
#### 根据地址栏获取某个参数（post和get形式都可以）
```JavaScript
/**
 * TP分页时，获取地址栏中为以下两种形式的某个参数
 * http://localhost/index.php/Personal/userInfo/type/ref/p/20.html
 * http://localhost/index.php/Personal/userInfo?type=refAll/p/2.html
 */
$(function(){
    function getUrlVars(){
        var vars = [], hash;
        var hashes=window.location.href.slice(window.location.href.indexOf('?')+1).split('&');
        for(var i = 0; i < hashes.length; i++) {
            hash = hashes[i].split('=');
            vars.push(hash[0]);
            vars[hash[0]] = hash[1];
        }
        return vars;
    }
    var broswerUrl = location.href;
	var re = broswerUrl.indexOf("?");
	var type = '';
	if(re == -1){	//-1：未查询到
        var arr = broswerUrl.split("/");//split:使用一个指定的分隔符把一个字符串分割存储到数组
        type = arr[7];//查看数组得知，需要的字符串下标为7
	}else{
        var params  = getUrlVars();
        type  = params[params[0]];
	}
    console.log(type);
});
```

### 时间戳转换成时间
```JavaScript
/**
 * 将时间戳转换成时间：若不传参，获取当前时间戳
 * @param times number 时间戳
 * @returns {string}   年-月-日 时:分:秒
 */
function getdate(times) {
    if(times){
        var time =Number(times +'000');   //将传入的时间戳增加3个0，变成微秒
        var now = new Date(time);
    }else{
        var now = new Date();
    }
    var y,m,d;
    y = now.getFullYear();
    m = now.getMonth() + 1;
    d = now.getDate();
    return y + "-" + (m < 10 ? "0" + m : m) + "-" + (d < 10 ? "0" + d : d) + " " + now.toTimeString().substr(0, 8);
}
```
### jQuery生成二维码
1：先引入jQuery插件文件
```JavaScript
<script type="text/JavaScript" src="../../public/js/jquery.qrcode.min.js"></script>
```
2：生成可以保存的二维码图片和不可保存的二维码
```JavaScript
$(function() {
    var str = window.location.href;
    var payUrl = str.slice(-35); //获取微信支付的url
    //不生成二维码
    $('#qrcode').qrcode(payUrl);
    $(".qrcodes").hide();
    //生成二维码图片，可保存至本地
   var qrcode= $('#qrcode').qrcode(payUrl).hide();
   var canvas=qrcode.find('canvas').get(0);
   $('#imgOne').attr('src',canvas.toDataURL('image/jpg'))
});
```
生成可保存至本地二维码图片的html
```
<img id='imgOne' src="">
```
### 使用prompt和confirm进行简单的页面交互
```javascript
function rec() {
    var name = prompt("请输入姓名", "");
    if(name != null){
        var sures = confirm('刚输入的姓名是：'+name);
        if(sures == true){
            alert('已记录');
        }else{
            alert('您已取消，请重新输入');
        }
	}
}
rec();
```
### 防止浏览器的返回上一页
```javascript
//在不可返回的页面添加下面这段JS
$(document).ready(function(e) {
var counter = 0;
if (window.history && window.history.pushState) {
    $(window).on('popstate', function () {
        window.history.pushState('forward', null, '#');
        window.history.forward(1);
        alert("不可回退");
    });
}

window.history.pushState('forward', null, '#'); //在IE中必须得有这两行
window.history.forward(1);
});
```
### 获取验证码倒计时
```html
<input class="weui-btn weui-btn_warn" id="getVerify" type="button" value="获取验证码" onclick="sendCode(this)">
```
```javascript
 function sendCode(thisBtn){
        var clock = '';
        var nums = 60;
        var btn;
        var mobile = $("#tel").val();
        if(mobile == '' || mobile.length != 11){
            weui.alert('请先输入正确的手机号');
        }else{
            btn = thisBtn;
            btn.disabled = true; //将按钮置为不可点击
            btn.value = '重新获取('+nums+')';
            clock = setInterval(doLoop, 1000); //一秒执行一次
            //调用接口
            $.ajax({
                url:"/api/app/user/getVerifyCode",
                type:"post",
                dataType:"json",
                data:{phone:mobile},
                success:function (data) {
                    if(data['status'] == 1){
                        weui.toast(data['msg']);
                    }else{
                        weui.alert(data['msg']);
                    }
                }
            });
        }
        function doLoop(){
            nums--;
            if(nums > 0){
                btn.value = '重新获取('+nums+')'
            }else{
                clearInterval(clock); //清除js定时器
                btn.disabled = false;
                btn.value = '获取验证码';
                nums = 60; //重置时间
            }
        }
    }
```

### 图片上传
```html
<input id="upFile" class="weui-uploader__input" type="file" accept="image/*" multiple/>
<img src="./src/images/pic_user_default.png" id="logo">
```
```javascript
$("#upFile").on("change",function () {
    var formData = new FormData();
    formData.append("file",$("#upFile")[0].files[0]);
    $.ajax({
        url: "/api/app/uploadFile",
        type: "post",
        dataType: "json",
        contentType: false, // 注意这里应设为false
        processData: false,
        cache: false,
        data: formData,
        success: function (data) {
            if(data["status"] == 1){
                var imgUrl = "http://106.14.148.77/api"+data["data"];
                $("#logo").attr("src",imgUrl);
            }
        }
    })
});
```

### js判断复选框是被选中
```html
<input type="checkbox" checked class="weui-agree__checkbox">
```

```javascript
var check   = $('input[class="weui-agree__checkbox"]').is(':checked');
alert(check == ture ? 'yes' : 'no');
```

### js判断几个复选框被选中
```html
<input type="checkbox" value="1" class="test">11
<input type="checkbox" value="2" class="test">21
<input type="checkbox" value="3" class="test">31
<input type="checkbox" value="4" class="test">41
<input type="checkbox" value="5" class="test">51
<input type="checkbox" value="6" class="test">61
<input type="checkbox" value="7" class="test">71
<button id="btn">OK</button>
```
```javascript
 $("#btn").click(function(){
    var chk_value = [];
    $('input[class="test"]:checked').each(function() {
        chk_value.push($(this).val());
    });
    if(chk_value.length == 0){
        alert('你还没有选择任何内容！');
    }else{
        alert(chk_value);
    }
    // alert(chk_value.length == 0 ? '你还没有选择任何内容！': chk_value);
});
```