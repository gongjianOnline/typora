# npm 基本操作

卸载指定包文件

```javascript
npm uninstall <依赖包名称>
```

升级指定包文件

```javascript
npm install xxx@latest 
```

---

## 开发依赖 & 生产依赖

- npm install xxx --save // -S
  - 生产依赖
- npm install xxx --dev // -D
  - 开发依赖

---

## 其他命令

```javascript
npm remove xxx // 删除指定包,同时也会删除该包在package.json中的声明
npm aduit fix // 监测项目依赖中的而一些问题,并且尝试修复
npm view xxx versions // 查看远程npm仓库中的所有版本信息
npm view xxx xersion // 查看仓库中xxx包的最新版本
npm ls xxx // 查看所安装的xxx包的所有版本
```



