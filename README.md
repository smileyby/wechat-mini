微信小程序踩坑全纪录
=================

### 微信小程序

微信小程序（weixinxiaochengxu），简称小程序，缩写XCX，英文名mini program，是一种不需要下载安装即可使用的应用，它实现了应用“触手可及”的梦想，用户扫一扫或搜一下即可打开应用。

最近项目需要做微信小程序的直播，就开始了小程序的踩坑之旅，下面主要讲一下在做项目的过程中遇到的一些问题和解决办法。

### 关于那些坑

* **上传图片：**wx.chooseImage 调用这个方法上传图片，返回的是微信存储的临时文件，如果想要永久保存这个临时文件需要调用`wx.saveFile`方法，传入临时文件路径，上传服务器则需要调用`wx.uploadFile`
* **上传图片**小程序上传图片不能批量上传，每次只能**单张上传**
* **上传音频：**wx.startRecord 调用这个方法录制音频，返回音频临时文件路径，在调用`wx.saveFile`方法将临时文件上传至服务器。这里要注意的是**微信录制的音频文件时.silk后缀的，需要在服务端进行转码**。（这里提供几种的后台转码方式：[https://www.xiaoweiba8.com/details.php?fkid=154&active=fk&type=%E5%89%8D%E7%AB%AF%E7%94%A8%E4%BE%8B&title=%E5%BE%AE%E4%BF%A1%E5%B0%8F%E7%A8%8B%E5%BA%8F%E5%BD%95%E9%9F%B3%E6%96%87%E4%BB%B6.silk%E4%B8%8A%E4%BC%A0%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%ACmp3%E6%A0%BC%E5%BC%8F](https://www.xiaoweiba8.com/details.php?fkid=154&active=fk&type=%E5%89%8D%E7%AB%AF%E7%94%A8%E4%BE%8B&title=%E5%BE%AE%E4%BF%A1%E5%B0%8F%E7%A8%8B%E5%BA%8F%E5%BD%95%E9%9F%B3%E6%96%87%E4%BB%B6.silk%E4%B8%8A%E4%BC%A0%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%ACmp3%E6%A0%BC%E5%BC%8F)，[http://www.cnblogs.com/wqh17/p/6911748.html](http://www.cnblogs.com/wqh17/p/6911748.html)）
* **audio**小程序的`audio`标签是不支持**3gp**格式的音频的，不支持不支持不支持
* **页面跳转**`navigator`标签和`navigatorTo`方法做跳转的时候，是不能跳转底部tab页面的，这么做是被小程序禁止的
* **background**小程序的在wxss中设置背景图时，不能使用本地图片，官方给出的解决办法是使用base64或者使用线上图片地址
* **第三方库文件**微信小程序中，使用**融云的jssdk不能使用**
* **request**使用`wx.request`方法接收到的数据，是小程序自己经过拼装的二次数据，里面添加了请求的状态，和小程序返回的状态码，服务端返回的数据统一被包裹在data对象中
* 页面链接带参数跳转，接收参数只能在onload函数中。一般为`options.要接收的数据`
* **拟态弹窗**小程序提供的状态弹窗，并没有给失败的弹窗，而且这里要吐槽下它的样式`toast`和`loading`两个弹窗自定义提示文字过多那丑的都没法看啊！！
* **数据驱动**小程序是基于数据驱动的，那么以前在网页端的那一套通过js来改变页面的表现实行不同的，这里完全通过修改数据来对页面的操作做相应的处理。并且当你修改数据后，小程序会自动帮你更新到页面中相应位置。（这点和angular、vue是相同的）
* **数据引用**app.js中的数据如果需要在其他页面被引用，小程序提供了`getApp()`这个方法，除了app.js,其他的js文件之间数据的引用只能是通过`wx.setStorage`存在缓存中
* **参数传递**页面中触发方法需要传参时，小程序提供了data属性，这是在元素身上，通过在发发内部`e.currentTarget.dataset.数据`来读取参数

* **获取用户授权** 注册的时候获取用户授权，如果用户第一次点击了拒绝，则下一次无法调起授权需要使用`wx.getSetting()`和`wx.openSetting()`两个函数来直接调起授权设置页面具体请参考：[小程序 setting 文档](https://mp.weixin.qq.com/debug/wxadoc/dev/api/setting.html)， [关于微信小程序拒绝授权后，重新授权并获取用户信息](http://www.cnblogs.com/commissar-Xia/p/6856951.html)

暂时就想到这么多，遇到新的问题在回来记录。（每天进步一点点）
