# h5端直接唤醒手机端APP

[关于Scheme的介绍](https://sspai.com/post/31500)

## 使用场景

在移动端web页面上,点击按钮直接打开手机端app,如果调用失败则前往对应app的应用市场.

## 实现方法

H5唤醒APP，需要使用 **scheme协议** (有app端提供)，但此协议在**微信**内置浏览器中是**无法调用**的，微信现在可以**通过微信开放标签实现**，所有在开始需要**判断设备**的环境和**浏览器**环境，如果是安卓或者IOS，在执行scheme协议。

## 下面代码以实现唤醒淘宝为例

```vue
<template>
	<div>
     	<button @click="satrtApp">打开淘宝APP</button>
    </div>
</template>
<script>
	export default {
        name:"demo",
        data(){
            return{
                
            }
        },
        methods:{
            startApp(){
                  var u = navigator.userAgent;
				var isWeixin = u.toLowerCase().indexOf('micromessenger') !== -1; // 微信内
				var isAndroid = u.indexOf('Android') > -1 || u.indexOf('Linux') > -1; //android终端
				var isIOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
                 if (isWeixin) {
				  alert('请在浏览器上打开')
				} else {
				  //android端
				  if (isAndroid) {
				    //安卓app的scheme协议
				    window.location.href = 'tbopen://m.taobao.com/tbopen/index.html?h5Url=这里放具体的地址';
				    setTimeout(function () {
				      let hidden = window.document.hidden || window.document.mozHidden || window.document.msHidden || window.document.webkitHidden
				      if (typeof hidden == "undefined" || hidden == false) {
				        //应用宝下载地址
				        window.location.href = "https://a.app.qq.com/o/simple.jsp?pkgname=com.taobao.taobao&g_f=1000047";
				      }
				    }, 2000);
				  }
				  //ios端
				  if (isIOS) {
				    //ios的scheme协议
				    window.location.href = 'taobao://s.taobao.com?id=商品id'
				    setTimeout(function () {
				      let hidden = window.document.hidden || window.document.mozHidden || window.document.msHidden || window.document.webkitHidden
				      if (typeof hidden == "undefined" || hidden == false) {
				        //App store下载地址
				        window.location.href = "http://itunes.apple.com/app/id387682726";
				      }
				    }, 2000);
				  }
				}
            }
        }
    }
</script>
```

---

## 总结一下大厂APP的scheme协议

### 应用类

[更多应用类scheme](https://sharecuts.cn/)

|     APP     |            scheme            |
| :---------: | :--------------------------: |
|    微博     |           weibo://           |
|     QQ      |            mqq://            |
|   支付宝    |          alipay://           |
|    微信     |  weixin://  或者 wechat://   |
| 微信-朋友圈 |     weixin://dl/moments      |
| 微信-公众号 | weixin://dl/officialaccounts |
|  微信-游戏  |      weixin://dl/games       |
|   chrome    |       googlechrome://        |
| 有道云笔记  |        youdaonote://         |
|  今日头条   |         snssdk141://         |
| 网易云音乐  |       orpheuswidget://       |
|  网易新闻   |          newsapp://          |
|   QQ音乐    |          qqmusic://          |
|  美团外卖   |       meituanwaimai://       |
|    美团     |         imeituan://          |
|  网易邮箱   |        neteasemail://        |
|   QQ邮箱    |          qqmail://           |
|  腾讯视频   |         tenvideo://          |
|   爱奇艺    |           iqiyi://           |
|    12306    |         cn.12306://          |
|    钉钉     |         dingtalk://          |
|  京东商城   |     openApp.jdMobile://      |

### 系统类

|    应用名称     |                    scheme                    |
| :-------------: | :------------------------------------------: |
|    app store    |                 itms-apps://                 |
|      短信       |                    sms://                    |
|      电话       |                    tel://                    |
|   无线局域网    |             App-Prefs:root=WIFI              |
|      蓝牙       |           App-Prefs:root=Bluetooth           |
|  蜂窝移动网络   |    App-Prefs:root=MOBILE_DATA_SETTINGS_ID    |
|    个人热点     |      App-Prefs:root=INTERNET_TETHERING       |
|     运营商      |            App-Prefs:root=Carrier            |
|      通知       |       App-Prefs:root=NOTIFICATIONS_ID        |
|  通用-关于本机  |      App-Prefs:root=General&path=About       |
|    通用-键盘    |     App-Prefs:root=General&path=Keyboard     |
|  通用-辅助功能  |  App-Prefs:root=General&path=ACCESSIBILITY   |
| 通用-语言与地区 |  App-Prefs:root=General&path=INTERNATIONAL   |
|    通用-还原    |             App-Prefs:root=Reset             |
|      Siri       |             App-Prefs:root=SIRI              |
|      隐私       |             App-Prefs:root=SIRI              |
|     Safari      |            App-Prefs:root=SAFARI             |
|      音乐       |             App-Prefs:root=MUSIC             |
|   音乐-均衡器   | App-Prefs:root=MUSIC&path=com.apple.Music:EQ |
|   照片与相机    |            App-Prefs:root=Photos             |
|    FaceTime     |           App-Prefs:root=FACETIME            |

