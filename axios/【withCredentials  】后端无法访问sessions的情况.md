# withCredentials 

## 定义
withCredentials  属性是一个Boolean类型，它指示了是否该使用类似cookies,authorization,headers(头部授权)或者TLS客户端证书这一类资格证书来创建一个跨站点访问控制（cross-site Access-Control）请求。在同一个站点下使用withCredentials属性是无效的。    
此外，这个指示也会被用做响应中cookies 被忽视的标示。默认值是false。

如果在发送来自其他域的XMLHttpRequest请求之前，未设置withCredentials 为true，那么就不能为它自己的域设置cookie值。而通过设置withCredentials 为true获得的第三方cookies，将会依旧享受同源策略，因此不能被通过document.cookie或者从头部相应请求的脚本等访问。

## 原生
```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://example.com/', true);
xhr.withCredentials = true;
xhr.send(null);
```

## axios
```javascript  
    axios.defaults.withCredentials = true;
```