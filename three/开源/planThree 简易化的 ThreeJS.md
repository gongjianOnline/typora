# planThree 简易化的 ThreeJS
<p align="center">
    <a href="">
        <img src="https://img.shields.io/badge/planThree-V1.0.5-orange" alt="uuid4">
    </a>
    <a href="">
        <img src="https://img.shields.io/badge/uuid4-2.0.3-brightgreen" alt="uuid4">
    </a>
    <a href="">
        <img src="https://img.shields.io/badge/three-0.145.0-brightgreen" alt="uuid4">
    </a>
    <a href="">
        <img src="https://img.shields.io/badge/vue-2.0-brightgreen" alt="uuid4">
    </a>
    <a href="">
        <img src="https://img.shields.io/badge/vue-3.0-brightgreen" alt="uuid4">
    </a>
    <a href="">
        <img src="https://img.shields.io/badge/tween-18.0-green" alt="uuid4">
    </a>
    <a href="">
        <img src="https://img.shields.io/badge/OSCS-%E5%AE%89%E5%85%A8%E8%AE%A4%E8%AF%81%E9%80%9A%E8%BF%87-blue" alt="uuid4">
    </a>
    <div style="text-align:center">
        <a href="https://www.murphysec.com/accept?code=9e9d3d9aec8fc757c0278170632ee97c&type=1&from=2&t=2" alt="Security Status"><img src="https://www.murphysec.com/platform3/v3/badge/1611442922361237504.svg" /></a>
    </div>

</p>
本插件是基于 ThreeJs 的二次封装, 可以让使用者通过API调用方式快速创建自己的Three场景, 工具库中提供了基础交互功能,更多功能持续更新中

## 安装

```shell
npm install plain-three -S
or 
yarn add plain-three -S
or
pnpm add plain-three
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

createSceneModule返回一个 promise 对象, 成功返回模型的实例对象（可用于模型实例的二次开发），失败则返回错误信息

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
    zoom:[0,0,0]
    userData: {
        name: "车辆",
        carCode: "京B2022",
    },
    moduleName: "车辆",
});
```

**在V1.0.4版本中，支持对模型的帧动画操作**；createParts返回一个 promise 对象,成功返回对象实例和关键帧动画实例 ,失败返回报错信息。

返回示例

```javascript
{
    gltf:{}, // 模型对象
    ItemAnimations:{ //动画属性
        id:'' , //生成当前该模型动画的uuid，
        example:"", // 当前的动画加载器实例
        animationAction：exampleItem.clipAction(gltf.animations[2], // 当前的动画源
    }
}
```

配置项

| 配置项     | 类型   | 是否必填 | 说明           |
| ---------- | ------ | -------- | -------------- |
| rootPath   | string | 是       | 模型根目录     |
| moduleFile | string | 是       | 模型文件名     |
| userData   | object | 是       | 自定义数据集合 |
| moduleName | string | 是       | 模型名称       |
| position   | array  | 是       | 模型位置       |
| zoom       | arrat  | 是       | 模型缩放       |

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
    url:"图片的CDN地址,推荐使用线上的资源",
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

---

### 10.外部模型的关键帧动画使用

参考第三节的创建物体后返回的实例对象

代码示例

```javascript
 var botany = await this.app.createParts({
     rootPath:"./threeModule/zhizhu/",
     moduleFile: "zhizhu.glb",
     position: [-15,5,20],
     zoom:[2,2,2],
     moduleName: "蜘蛛",
})
console.log("蜘蛛模型",botany) // 此时的变量已经返回了模型实例和动画实例
//播放帧动画
let {exampleItem,ItemAnimations} = botany.animation;
ItemAnimations.animationAction = exampleItem.clipAction(this.insect.gltf.animations[index])
ItemAnimations.animationAction.play()

/*如果出去动画停止，或者切换动画时，需要调用stop()停止当前的动画*/
ItemAnimations.animationAction.stop()
```

### 11. HTML标注信息

```javascript
this.app.createHtml(options)
```

参数

| 参数     | 类型                     | 是否选填 | 描述                               |
| -------- | ------------------------ | -------- | ---------------------------------- |
| HTMLId   | string                   | 是       | 传递模型的uuid，用于html的唯一标识 |
| Mesh     | object                   | 是       | 模型的实例对象                     |
| position | array[x:number,y:number] | 是       | 用于附加HTML浮窗的位置             |

示例

```javascript
const moduleHtmlData = []; // 用于存放每个模型的id和数据，用于HTML循环创建元素
var botany = await this.app.createParts(options) //创建外部模型
var {scene} = this.botany.gltf;
moduleHtmlData.push({
    uuid:scene.uuid,
    htmlValue:{
      title:"xxx",
      w:"xxx",
      w2:"xxx"
    }
})
this.app.createHtml({
    HTMLId:scene.uuid,
    Mesh:scene,
    position:[40,-130]
})
```
