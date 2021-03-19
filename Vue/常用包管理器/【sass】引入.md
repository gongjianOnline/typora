# sass引入

```javascript
"sass-loader": "7.3.1",
"style-loader": "^2.0.0",   
"node-sass":'4.0.0'
```

---

```javascript
build/webpack.base.conf/
module:{
    rules:[
        {
        	test: /.sass$/,
        	loaders: ['style', 'css', 'sass']
      	},
    ]
}
```

