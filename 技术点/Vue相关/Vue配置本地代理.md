# Vue配置本地代理

```javascript
module.exports = {
    dev:{
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',
        proxyTable:{
            '/scoket': {
                target: 'xxx',
                changeOrigin: true,
                secure: false,
                pathRewrite: {
                    '^/scoket': ''
                }
            },
        }
    }
}
```

