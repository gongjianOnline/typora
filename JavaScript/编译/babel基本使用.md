# babel安装与使用

## 安装

```javascript
npm install babel-preset-es2015 --save-dev
npm install babel-cli -g
```

## 配置

新建文件名为  **.babelrc** 并配置

```javascript
{
    "presets":[
        "es2015"
    ],
    "plugins":[]
}
```

## 使用

```javascript
babel es6的文件夹名称 -d es5的文件夹名称
```

