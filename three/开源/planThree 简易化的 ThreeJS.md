# planThree 简易化的 ThreeJS

本工具库仅限于学习使用，如进行商务用途出现问题作者概不负责

本插件是基于 ThreeJs 的二次封装, 可以让使用者通过API调用方式快速创建自己的Three场景, 工具库中提供了基础交互功能,更多功能持续更新中

[github地址](https://github.com/gongjianOnline/PlainThree)    [码云](https://gitee.com/gongjianweb/plain-three)    [npm地址](https://www.npmjs.com/package/plain-three)

## 安装

```shell
npm install plain-three -S
or 
yarn add plain-three -S
```

## 引入

```javascript
import PlainThree from "plain-three"
```

## 使用文档

### 1. 实例化实例

代码实例

```javascript
const app = PlainThree({
    elementId: "scene",
    cameraPosition:[50,50,130],
    FPS:120,
    logarithmicDepthBuffer:true,
    physicallyCorrectLights:true
});
```

配置说明

| 配置项                  | 类型          | 是否必填 | 说明                       |
| ----------------------- | ------------- | -------- | -------------------------- |
| elementId               | string        | 是       | 实例挂载的HTML元素的id     |
| cameraPosition          | array[number] | 否       | 相机的位置                 |
| FPS                     | number        | 否       | 模型渲染的帧率 默认10      |
| logarithmicDepthBuffer  | boolean       | 否       | 开启性能优化. 默认为 false |
| physicallyCorrectLights | boolean       | 否       | 开启物理光照 默认为 false  |

---

### 2. 创建场景模型

代码示例

```javascript
 await app.createSceneModule({
     rootPath: "./module/",
     moduleFile: "xxx.gltf",
     userData: {
         key: "模型属性",
         key2: "模型属性2",
         ...
     },
     moduleName: "模型名称",
})
```

createSceneModule返回一个 promise 对象,成功返回 true ,失败返回报错信息

配置项

| 配置项     | 类型   | 是否必填 | 说明             |
| ---------- | ------ | -------- | ---------------- |
| rootPath   | string | 是       | 配置路径的根目录 |
| moduleFile | string | 是       | 配置路径的文件   |
| userData   | object | 是       | 自定义数据集合   |
| moduleName | string | 是       | 模型名称         |

---

### 3.创建物体

示例

```javascript
await app.createParts({
    rootPath: "/car/",
    moduleFile: "acura-rlx-2021.quads.gltf",
    position: [0, 1, 0],
    userData: {
        name: "车辆",
        carCode: "京B2022",
    },
    moduleName: "车辆",
});
```

createParts返回一个 promise 对象,成功返回 true ,失败返回报错信息, 

配置项

| 配置项     | 类型   | 是否必填 | 说明           |
| ---------- | ------ | -------- | -------------- |
| rootPath   | string | 是       | 模型根目录     |
| moduleFile | string | 是       | 模型文件名     |
| userData   | object | 是       | 自定义数据集合 |
| moduleName | string | 是       | 模型名称       |
| position   | array  | 是       | 模型位置       |

---

### 4. 查找物体

示例

```javascript
app.getQuery("车辆")
```

配置项

| 配置项 | 类型   | 是否必填 | 说明                   |
| ------ | ------ | -------- | ---------------------- |
|        | string | 是       | 查找模型的moduleName值 |

**注意: 改方法只有在模型渲染完成后才可以调用**

---

### 5.创建标注

示例

```javascript
app.createMarker({
    name:'车辆',
    url:'图片的CDN地址,推荐使用线上的资源"
    position:[0,2,5],
    scale:[1,1,1],
    userData:{
        name:"车辆的标注"
    },
    thingData:this.app.getQuery("车辆")
})
```

配置项

| 配置项    | 类型          | 是否必填 | 说明                             |
| --------- | ------------- | -------- | -------------------------------- |
| alias     | string        | 是       | 标注的别名                       |
| thingData | object        | 是       | 表示标注物体父集组对象           |
| name      | string        | 是       | 需要标注的物体的名称             |
| url       | string        | 是       | 图片地址(CDN方式)                |
| position  | array[number] | 是       | 标注的偏移量[x,y,z]              |
| scale     | array[number] | 是       | 图片的缩放大小[x,y,z]            |
| userData  | object        | 否       | 预留属性用于存放标注的自定义属性 |

---

### 6. 隐藏标注

示例

```javascript
app.clearMarker({name:this.deviceName})
```

参数配置

| 参数 | 类型   | 是否必填 | 说明                                                         |
| ---- | ------ | -------- | ------------------------------------------------------------ |
| name | string | 是       | 查找标注的name (标准的类型是Sprite, 位置在全局的group的children集合里面), 例如:  xxx.obj.parent.name |

---

### 7. 隐藏物体

示例

```javascript
let state = app.getQuery("车辆").visible
app.hideThing("车辆",!state)
```

参数配置

| 参数  | 类型   | 是否必填 | 说明               |
| ----- | ------ | -------- | ------------------ |
| name  | string | 是       | 模型的moduleName值 |
| state | boole  | 是       | 模型是否隐藏       |

---

### 8. 清除场景

示例

```javascript
app.clearScene();
```

---

### 9.注册点击事件

示例

```javascript
window.addEventListener("dblclick", (event) => {
  	let result = this.app.click(event);
    console.log("点击事件", result);
});
```

配置项

| 参数  | 类型   | 是否必填 | 说明                           |
| ----- | ------ | -------- | ------------------------------ |
| event | object | 是       | 点击是触发的HTMLDOM的event对象 |

返回值 **eventInfo**

| 属性名 | 类型          | 说明                          |
| ------ | ------------- | ----------------------------- |
| obj    | object        | 当前鼠标点击拾取物体          |
| point  | array[number] | 当前鼠标点击的场景坐标[x,y,z] |