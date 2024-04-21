# electron主进程操作静态资源打包后报错处理

## 场景描述

```file
project
|--resources
	|--test.json
```

在主进程中通过 `fs` 模块读取 `test.json` 的内容，开发环境可以读取相对路径文件获取，生产环境此文件路径会发生变化会导致读取失败，报错信息如下

![](https://gjweb.top/wp-content/uploads/2024/04/a1f3e7bdd45b0816b35516f14f0a3e8-1024x331.png)

是因为打包后的安装在电脑中，此时项目中的路径已经变成 `resources/app.asar.unpacked/resources/test.json`

## 解决方法

在 vite 中设置环境变量，让其自动切换开发和生产环境的路径

在根目录创建 `.env.development` 和 `.env.production` 分别用于生产环境和开发环境

```config
/*.env.development*/
MAIN_VITE_PYBASH=""
/*.env.production*/
MAIN_VITE_PYBASH="resources/app.asar.unpacked/"
```

在主进程中使用

```ts
fs.readFile(`${import.meta.env.MAIN_VITE_PYBASH}resources/test.json`,(_,data)=>{
    console.log(data)
})
```

**在 vite-electron 设置环境变量需要命名规范**

```js
// .env
KEY=123                # 无效变量
MAIN_VITE_KEY=123      # 仅主进程可用
PRELOAD_VITE_KEY=123   # 仅预加载脚本可用
RENDERER_VITE_KEY=123  # 仅渲染器可用
VITE_KEY=123           # 所有进程共用
```

