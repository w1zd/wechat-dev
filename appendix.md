# 附录

## JS-SDK使用权限签名算法
生成签名之前必须先了解一下`jsapi_ticket`，`jsapi_ticket`是公众号用于调用微信JS接口的临时票据。正常情况下，`jsapi_ticket`的有效期为`7200`秒，通过`access_token`来获取。由于获取`jsapi_ticket`的api调用次数非常有限，频繁刷新`jsapi_ticket`会导致api调用受限，影响自身业务，开发者必须在自己的服务全局缓存`jsapi_ticket` 。

### 获取jsapi_ticket
1. 参考以下文档获取`access_token`（有效期7200秒，开发者必须在自己的服务全局缓存`access_token`)[`access_token`的获取方式](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140183)

2. 用第一步拿到的`access_token` 采用`http GET`方式请求获得`jsapi_ticket`（有效期7200秒，开发者必须在自己的服务全局缓存`jsapi_ticket`）：https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token=ACCESS_TOKEN&type=jsapi

成功返回如下JSON：
```json
{
    "errcode":0,
    "errmsg":"ok",
    "ticket":"bxLdikRXVbTPdHSM05e5u5sUoXNKd8-41ZO3MhKoyN5OfkWITDGgnr2fwJ0m9E8NYzWKVZvdVtaUgWvsdshFKA",
    "expires_in":7200
}
```
获得`jsapi_ticket`之后，就可以生成JS-SDK权限验证的签名了。

### 签名算法

签名生成规则如下：参与签名的字段包括`noncestr`（随机字符串）, 有效的`jsapi_ticket`, `timestamp`（时间戳）, `url`（当前网页的URL，不包含#及其后面部分） 。对所有待签名参数按照字段名的`ASCII` 码从小到大排序（字典序）后，使用URL键值对的格式（即`key1=value1&key2=value2…`）拼接成字符串`string1`。这里需要注意的是所有参数名均为小写字符。对`string1`作`sha1`加密，字段名和字段值都采用原始值，不进行URL 转义。


即`signature=sha1(string1)` 示例：
```text
noncestr=Wm3WZYTPz0wzccnW
jsapi_ticket=sM4AOVdWfPE4DxkXGEs8VMCPGGVi4C3VM0P37wVUCFvkVAy_90u5h9nbSlYy3-Sl-HhTdfl2fzFy1AOcHKP7qg
timestamp=1414587457
url=http://mp.weixin.qq.com?params=value
```
<p class='tip'>步骤1. 对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1：</p>
```text
jsapi_ticket=sM4AOVdWfPE4DxkXGEs8VMCPGGVi4C3VM0P37wVUCFvkVAy_90u5h9nbSlYy3-Sl-HhTdfl2fzFy1AOcHKP7qg&noncestr=Wm3WZYTPz0wzccnW×tamp=1414587457&url=http://mp.weixin.qq.com?params=value

```
<p class='tip'>步骤2. 对string1进行sha1签名，得到signature：</p>

```text
0f9de62fce790f9a083d5c99e95740ceb90c27ed
```

### 注意事项

<p class="danger">1. 签名用的noncestr和timestamp必须与wx.config中的nonceStr和timestamp相同。</p>

<p class="danger">2. 签名用的url必须是调用JS接口页面的完整URL。</p>

<p class="danger">3. 出于安全考虑，开发者必须在服务器端实现签名的逻辑。</p>


## JS-SDK接口列表
onMenuShareTimeline

onMenuShareAppMessage

onMenuShareQQ

onMenuShareWeibo

onMenuShareQZone

startRecord

stopRecord

onVoiceRecordEnd

playVoice

pauseVoice

stopVoice

onVoicePlayEnd

uploadVoice

downloadVoice

chooseImage

previewImage

uploadImage

downloadImage

translateVoice

getNetworkType

openLocation

getLocation

hideOptionMenu

showOptionMenu

hideMenuItems

showMenuItems

hideAllNonBaseMenuItem

showAllNonBaseMenuItem

closeWindow

scanQRCode

chooseWXPay

openProductSpecificView

addCard

chooseCard

openCard

## 常见错误及解决方法

常见错误及解决方法
调用config 接口的时候传入参数 debug: true 可以开启debug模式，页面会alert出错误信息。以下为常见错误及解决方法：

1. invalid url domain当前页面所在域名与使用的appid没有绑定，请确认正确填写绑定的域名，仅支持80（http）和443（https）两个端口，因此不需要填写端口号（一个appid可以绑定三个有效域名，见 ]目录1.1.1）。

2. invalid signature签名错误。建议按如下顺序检查：

    1. 确认签名算法正确，可用http://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=jsapisign 页面工具进行校验。

    2. 确认config中nonceStr（js中驼峰标准大写S）, timestamp与用以签名中的对应noncestr, timestamp一致。

    3. 确认url是页面完整的url(请在当前页面alert(location.href.split('#')[0])确认)，包括'http(s)://'部分，以及'？'后面的GET参数部分,但不包括'#'hash后面的部分。

    4. 确认 config 中的 appid 与用来获取 jsapi_ticket 的 appid 一致。

    5. 确保一定缓存access_token和jsapi_ticket。

    6. 确保你获取用来签名的url是动态获取的，动态页面可参见实例代码中php的实现方式。如果是html的静态页面在前端通过ajax将url传到后台签名，前端需要用js获取当前页面除去'#'hash部分的链接（可用location.href.split('#')[0]获取,而且需要encodeURIComponent），因为页面一旦分享，微信客户端会在你的链接末尾加入其它参数，如果不是动态获取当前链接，将导致分享后的页面签名失败。

3. the permission value is offline verifying这个错误是因为config没有正确执行，或者是调用的JSAPI没有传入config的jsApiList参数中。建议按如下顺序检查：

    1. 确认config正确通过。

    2. 如果是在页面加载好时就调用了JSAPI，则必须写在wx.ready的回调中。

    3. 确认config的jsApiList参数包含了这个JSAPI。

4. permission denied该公众号没有权限使用这个JSAPI，或者是调用的JSAPI没有传入config的jsApiList参数中（部分接口需要认证之后才能使用）。

5. function not exist当前客户端版本不支持该接口，请升级到新版体验。

6. 为什么6.0.1版本config:ok，但是6.0.2版本之后不ok（因为6.0.2版本之前没有做权限验证，所以config都是ok，但这并不意味着你config中的签名是OK的，请在6.0.2检验是否生成正确的签名以保证config在高版本中也ok。）

7. 在iOS和Android都无法分享（请确认公众号已经认证，只有认证的公众号才具有分享相关接口权限，如果确实已经认证，则要检查监听接口是否在wx.ready回调函数中触发）

8. 服务上线之后无法获取jsapi_ticket，自己测试时没问题。（因为access_token和jsapi_ticket必须要在自己的服务器缓存，否则上线后会触发频率限制。请确保一定对token和ticket做缓存以减少2次服务器请求，不仅可以避免触发频率限制，还加快你们自己的服务速度。目前为了方便测试提供了1w的获取量，超过阀值后，服务将不再可用，请确保在服务上线前一定全局缓存access_token和jsapi_ticket，两者有效期均为7200秒，否则一旦上线触发频率限制，服务将不再可用）。

9. uploadImage怎么传多图（目前只支持一次上传一张，多张图片需等前一张图片上传之后再调用该接口）

10. 没法对本地选择的图片进行预览（chooseImage接口本身就支持预览，不需要额外支持）

11. 通过a链接(例如先通过微信授权登录)跳转到b链接，invalid signature签名失败（后台生成签名的链接为使用jssdk的当前链接，也就是跳转后的b链接，请不要用微信登录的授权链接进行签名计算，后台签名的url一定是使用jssdk的当前页面的完整url除去'#'部分）

12. 出现config:fail错误（这是由于传入的config参数不全导致，请确保传入正确的appId、timestamp、nonceStr、signature和需要使用的jsApiList）

13. 如何把jsapi上传到微信的多媒体资源下载到自己的服务器（请参见文档中uploadVoice和uploadImage接口的备注说明）

14. Android通过jssdk上传到微信服务器，第三方再从微信下载到自己的服务器，会出现杂音（微信团队已经修复此问题，目前后台已优化上线）

15. 绑定父级域名，是否其子域名也是可用的（是的，合法的子域名在绑定父域名之后是完全支持的）

16. 在iOS微信6.1版本中，分享的图片外链不显示，只能显示公众号页面内链的图片或者微信服务器的图片，已在6.2中修复

17. 是否需要对低版本自己做兼容（jssdk都是兼容低版本的，不需要第三方自己额外做更多工作，但有的接口是6.0.2新引入的，只有新版才可调用）

18. 该公众号支付签名无效，无法发起该笔交易（请确保你使用的jweixin.js是官方线上版本，不仅可以减少用户流量，还有可能对某些bug进行修复，拷贝到第三方服务器中使用，官方将不对其出现的任何问题提供保障，具体支付签名算法可参考 JSSDK微信支付一栏）

19. 目前Android微信客户端不支持pushState的H5新特性，所以使用pushState来实现web app的页面会导致签名失败，此问题已在Android6.2中修复

20. uploadImage在chooseImage的回调中有时候Android会不执行，Android6.2会解决此问题，若需支持低版本可以把调用uploadImage放在setTimeout中延迟100ms解决

21. require subscribe错误说明你没有订阅该测试号，该错误仅测试号会出现

22. getLocation返回的坐标在openLocation有偏差，因为getLocation返回的是gps坐标，openLocation打开的腾讯地图为火星坐标，需要第三方自己做转换，6.2版本开始已经支持直接获取火星坐标

23. 查看公众号（未添加）: "menuItem:addContact"不显示，目前仅有从公众号传播出去的链接才能显示，来源必须是公众号

24. ICP备案数据同步有一天延迟，所以请在第二日绑定