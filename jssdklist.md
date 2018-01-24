# JS-SDK 列表

## JS-SDK Demo
这个demo主要用来查看代码，看效果可以用微信官方提供的开发工具进行调试
[demo地址：http://203.195.235.76/jssdk/](http://203.195.235.76/jssdk/)

## 基础接口
基础接口可以用来判断当前微信客户端是否支持指定的JS接口
```js
wx.checkJsApi({
    jsApiList: ['chooseImage'], // 需要检测的JS接口列表，所有JS接口列表见附录2,
    success: function(res) {
        // 以键值对的形式返回，可用的api值true，不可用为false
        // 如：{"checkResult":{"chooseImage":true},"errMsg":"checkJsApi:ok"}
    }
});
```
## 分享接口
### 获取“分享到朋友圈”按钮点击状态及自定义分享内容接口
```js
wx.onMenuShareTimeline({
    title: '', // 分享标题
    link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: '', // 分享图标
    success: function () {
    // 用户确认分享后执行的回调函数
},
cancel: function () {
    // 用户取消分享后执行的回调函数
    }
});
```

### 获取“分享给朋友”按钮点击状态及自定义分享内容接口
```js
wx.onMenuShareAppMessage({
    title: '', // 分享标题
    desc: '', // 分享描述
    link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: '', // 分享图标
    type: '', // 分享类型,music、video或link，不填默认为link
    dataUrl: '', // 如果type是music或video，则要提供数据链接，默认为空
    success: function () {
    // 用户确认分享后执行的回调函数
    },
    cancel: function () {
    // 用户取消分享后执行的回调函数
    }
});
```

### 获取“分享到QQ”按钮点击状态及自定义分享内容接口
```js
wx.onMenuShareQQ({
    title: '', // 分享标题
    desc: '', // 分享描述
    link: '', // 分享链接
    imgUrl: '', // 分享图标
    success: function () {
    // 用户确认分享后执行的回调函数
    },
    cancel: function () {
    // 用户取消分享后执行的回调函数
    }
});
```

### 获取“分享到腾讯微博”按钮点击状态及自定义分享内容接口
```js
wx.onMenuShareWeibo({
    title: '', // 分享标题
    desc: '', // 分享描述
    link: '', // 分享链接
    imgUrl: '', // 分享图标
    success: function () {
    // 用户确认分享后执行的回调函数
    },
    cancel: function () {
    // 用户取消分享后执行的回调函数
    }
});
```

### 获取“分享到QQ空间”按钮点击状态及自定义分享内容接口
```js
wx.onMenuShareQZone({
    title: '', // 分享标题
    desc: '', // 分享描述
    link: '', // 分享链接
    imgUrl: '', // 分享图标
    success: function () {
    // 用户确认分享后执行的回调函数
    },
    cancel: function () {
    // 用户取消分享后执行的回调函数
    }
});
```
## 图像接口
### 拍照或从手机相册中选图接口
```js
wx.chooseImage({
    count: 1, // 默认9
    sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
    sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
    success: function (res) {
    var localIds = res.localIds; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
    }
});
```

### 预览图片接口
```js
wx.previewImage({
    current: '', // 当前显示图片的http链接
    urls: [] // 需要预览的图片http链接列表
});
```

### 上传图片接口
```js
wx.uploadImage({
    localId: '', // 需要上传的图片的本地ID，由chooseImage接口获得
    isShowProgressTips: 1, // 默认为1，显示进度提示
    success: function (res) {
    var serverId = res.serverId; // 返回图片的服务器端ID
    }
});
```
<p class="warning">备注：上传图片有效期3天，可用微信多媒体接口下载图片到自己的服务器，此处获得的 serverId 即 media_id。</p>


### 下载图片接口
```js
wx.downloadImage({
    serverId: '', // 需要下载的图片的服务器端ID，由uploadImage接口获得
    isShowProgressTips: 1, // 默认为1，显示进度提示
    success: function (res) {
    var localId = res.localId; // 返回图片下载后的本地ID
    }
});
```

### 获取本地图片接口
```js
wx.getLocalImgData({
    localId: '', // 图片的localID
    success: function (res) {
    var localData = res.localData; // localData是图片的base64数据，可以用img标签显示
    }
});
```
<p class="warning">备注：此接口仅在 iOS WKWebview 下提供，用于兼容 iOS WKWebview 不支持 localId 直接显示图片的问题。具体可参考[《iOS网页开发适配指南》](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1483682025_enmey)</p>


## 音频接口
### 开始录音接口
```js
wx.startRecord();
```

### 停止录音接口
```js
wx.stopRecord({
    success: function (res) {
        var localId = res.localId;
    }
});
```

### 监听录音自动停止接口
```js
wx.onVoiceRecordEnd({
    // 录音时间超过一分钟没有停止的时候会执行 complete 回调
    complete: function (res) {
        var localId = res.localId;
    }
});
```

### 播放语音接口
```js
wx.playVoice({
    localId: '' // 需要播放的音频的本地ID，由stopRecord接口获得
});
```

### 暂停播放接口
```js
wx.pauseVoice({
    localId: '' // 需要暂停的音频的本地ID，由stopRecord接口获得
});
```

### 停止播放接口
```js
wx.stopVoice({
    localId: '' // 需要停止的音频的本地ID，由stopRecord接口获得
});
```

### 监听语音播放完毕接口
```js
wx.onVoicePlayEnd({
    success: function (res) {
    var localId = res.localId; // 返回音频的本地ID
    }
});
```

### 上传语音接口
```js
wx.uploadVoice({
    localId: '', // 需要上传的音频的本地ID，由stopRecord接口获得
    isShowProgressTips: 1, // 默认为1，显示进度提示
    success: function (res) {
        var serverId = res.serverId; // 返回音频的服务器端ID
    }
});
```
<p class="warning">备注：上传语音有效期3天，可用微信多媒体接口下载语音到自己的服务器，此处获得的 serverId 即 media_id，参考文档 .目前多媒体文件下载接口的频率限制为10000次/天，如需要调高频率，请登录微信公众平台，在开发 - 接口权限的列表中，申请提高临时上限。</p>

### 下载语音接口
```js
wx.downloadVoice({
    serverId: '', // 需要下载的音频的服务器端ID，由uploadVoice接口获得
    isShowProgressTips: 1, // 默认为1，显示进度提示
    success: function (res) {
        var localId = res.localId; // 返回音频的本地ID
    }
});
```

## 智能接口
### 识别音频并返回识别结果接口
```js
wx.translateVoice({
    localId: '', // 需要识别的音频的本地Id，由录音相关接口获得
    isShowProgressTips: 1, // 默认为1，显示进度提示
    success: function (res) {
        alert(res.translateResult); // 语音识别的结果
    }
});
```

## 设备信息
### 获取网络状态接口
```js
wx.getNetworkType({
    success: function (res) {
        var networkType = res.networkType; // 返回网络类型2g，3g，4g，wifi
    }
});
```
## 地理位置
### 使用微信内置地图查看位置接口
```js
wx.openLocation({
    latitude: 0, // 纬度，浮点数，范围为90 ~ -90
    longitude: 0, // 经度，浮点数，范围为180 ~ -180。
    name: '', // 位置名
    address: '', // 地址详情说明
    scale: 1, // 地图缩放级别,整形值,范围从1~28。默认为最大
    infoUrl: '' // 在查看位置界面底部显示的超链接,可点击跳转
});
```

### 获取地理位置接口
```js
wx.getLocation({
    type: 'wgs84', // 默认为wgs84的gps坐标，如果要返回直接给openLocation用的火星坐标，可传入'gcj02'
    success: function (res) {
        var latitude = res.latitude; // 纬度，浮点数，范围为90 ~ -90
        var longitude = res.longitude; // 经度，浮点数，范围为180 ~ -180。
        var speed = res.speed; // 速度，以米/每秒计
        var accuracy = res.accuracy; // 位置精度
    }
});
```


## 界面操作
### 关闭当前网页窗口接口
```js
wx.closeWindow();
```

### 批量隐藏功能按钮接口
```js
wx.hideMenuItems({
    menuList: [] // 要隐藏的菜单项，只能隐藏“传播类”和“保护类”按钮，所有menu项见附录3
});
```

### 批量显示功能按钮接口
```js
wx.showMenuItems({
    menuList: [] // 要显示的菜单项，所有menu项见附录3
});
```

### 隐藏所有非基础按钮接口
```js
wx.hideAllNonBaseMenuItem();
// “基本类”按钮详见附录3
```

### 显示所有功能按钮接口
```js
wx.showAllNonBaseMenuItem();
```
## 微信支付
微信支付接口必须是认证服务号才可以使用,且微信支付有完整的一套文档可以查阅。
[微信支付开发文档地址](https://pay.weixin.qq.com/wiki/doc/api/index.html)
