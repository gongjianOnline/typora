# react项目初始化
1. 优化import
2. 使用styled-components
3. 统一css
4. React-router
5. 使用SVG图片
6. 



## 1.优化import
在tscinfig.json中添加,可以吧src当做根目录路直接import "app.tsx"
```json
    "compilerOptions": {
        +  "baseUrl": "src",
    }
```

## 2. 使用styled-components
使用styled-components进行css

安装
```javascript
    yarn add styled-components
    yarn add --dev @types/styled-components
```
使用
```javascript
    import styled from 'styled-components'
    const Button = styled.button`
      color:red
    `
    const App = ()=>{
        return (
            <Button></Button>
        )
    }
```

## 3. 统一css

在scss或者css文件中添加
```javascript
    @ihelpermport-normalize;
```

## 4. React-router
[参考文档](https://reactrouter.com/web/guides/quick-start)

设置404页面
```javascript
    <Switch>
        <Route path="*">
            404,页面不存在
        </Route>
    </Switch>
```

设置默认显示页
```javascript
    <Switch>
        <Redirect exact from="/" to="/money">
    </Switch>
```



## 5. 跨平台CSS中文字体解决方案

[中文字体 css github](https://zenozeng.github.io/fonts.css/)

## 6. 使用SVG图片
如果在项目中使用SVG图片,需要借助svg-sprite-loader进行编译
配置方法如下
1. 显示react的配置文件
```javascript   
    yarn eject
```
2. 安装svg-sprite-loader
```javascript   
    yarn add --dev svg-sprite-loader
    yarn add --dev svgo-loader
    
```
3. 配置config/webpack.config.js
```javascript
    oneOf:[
        {
          test: /\.svg$/,
          use: [
            { loader: 'svg-sprite-loader', options: {} },
            {loader:'svgo-loader',options:{}}
          ]
        }
    ]
```
svgo-loader:SVG优化
svg-transform-loader: 对SVG图片进行优化(不需要)

4.配置完成后需要重新运行项目
```javascript
    yarn start
```
5. 引入SVG图片
```javascript   
    require("../icons/test.svg")
    <svg fill="red" className="className">
        <use xlinkHref="#test(文件名)"/>
    <svg>
```
==如果使用import xx from ""引入会触发TreeShaking机制,react会自动删除==