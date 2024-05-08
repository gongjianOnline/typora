# 音频可视化 audio-visualization-pro

![](https://img.shields.io/badge/npm-v_9.51-a) ![](https://img.shields.io/badge/vue-v_2.0-blue) ![](https://img.shields.io/badge/vue-v_3.0-blue) ![](https://img.shields.io/badge/react-v_18.0-red) ![](https://img.shields.io/badge/audio-visualization_pro-a)

## 简介

`audio-visualization-pro` 是针对 `audio-visualization`开源的强化版本,通过 `WEB-AUDIO-API` 实现的实现的音乐可视化的库，通过本库你可以获取到`当前音频`的音频频谱，并在原作者基础上强化了创建、暂停、销毁等实例方法并提供相应的 API 方法

## 地址

[github地址](https://github.com/gongjianOnline/audio-visualization)  [audio-visualization-pro](https://www.npmjs.com/package/audio-visualization-pro)

## 安装

```shell
npm install audio-visualization-pro   
yarn add audio-visualization-pro   
pnpm install audio-visualization-pro   
```

## 方法

| 方法                                             | 注释                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| AudioVisualizationPro(url:string,loop:booler)    | 实例化API                                                    |
| setFftSzie(int)                                  | 音频振幅常用32、64、128、512（等倍数定义）                   |
| play(({frequency, currentTime, audiostate})=>{}) | 播放，包含回到函数 frequency 音频振幅数组 Array[number]、currentTime播放的进度、audiostate播放状态 1播放、0停止 |
| suspend()                                        | 暂停播放                                                     |
| destroy()                                        | 销毁当前实例，可重新创建新的音乐可视化实例                   |

## 效果

![](https://gjweb.top/wp-content/uploads/2024/05/1715153431922-00_00_00-00_00_30.gif)

## 示例

### vue2.0 

```vue
<template>
  <div id="app">
    <div class="titleContainer">音频可视化</div>
    <div class="echartsContainer" id="echartsContainer"></div>
    <!-- 控制器 -->
    <div class="controllerContainer">
      <div @click="audioPlay(1)">播放</div>
      <div @click="audioPlay(0)">暂停</div>
      <div @click="destruction()">销毁实例重新创建</div>
    </div>
  </div>
</template>

<script>
import "audio-visualization-pro"
export default {
  name: 'App',
  components: {
    
  },
  data(){
    return {
      AVP:null
    }
  },
  mounted(){
    this.createVisualization();
  },
  methods:{
    /**初始化实例 */
    createVisualization(){
      this.AVP = new window.AudioVisualizationPro({
        url:require("./assets/audio.mp3"),
        loop:false
      })
    },
    /**播放&暂停 */
    audioPlay(type){
      if(type){
        this.AVP.play(({frequency})=>{
          this.createEcharts([...Array.from(frequency).reverse(), ...frequency])
        })
      }else{
        this.AVP.suspend()
      }
    },
    /*销毁实例*/
    destruction(){
      if(this.AVP){
        this.AVP.destroy();
        this.AVP = null;
        this.createVisualization()
      }
    },
    /*图表*/
	createEcharts(data){}
  }
}
</script>
```

### vue3.0

```vue
<template>
  <div id="app">
    <div class="titleContainer">音频可视化</div>
    <div class="echartsContainer" id="echartsContainer"></div>
    <!-- 控制器 -->
    <div class="controllerContainer">
      <div @click="audioPlay(1)">播放</div>
      <div @click="audioPlay(0)">暂停</div>
      <div @click="destruction()">销毁实例重新创建</div>
    </div>
  </div>
</template>
<script setup lang="ts">
import "audio-visualization-pro"
import { onMounted } from "vue";
import audioAssets from "./assets/audio.mp3";

  onMounted(()=>{
    createVisualization()
  })

  let AVP:any = null;
  /**初始化实例 */
  const createVisualization = ()=>{
    AVP = new (window as any).AudioVisualizationPro({
      url:audioAssets,
      loop:false
    })
  };
  /**播放&暂停 */
  const audioPlay = (type:number)=>{
    if(type){
      // currentTime . audiostate
      AVP.play(({frequency}:any)=>{
        createEcharts([...Array.from(frequency).reverse(), ...frequency])
      })
    }else{
      AVP.suspend()
    }
  };
  /*销毁实例*/
  const destruction = ()=>{
    if(AVP){
      AVP.destroy();
      AVP = null;
      createVisualization()
    }
  };
  /**实例化Echarts */
  const createEcharts = (data:any)=>{}


</script>
```

### react

```tsx
import './App.css'
import "audio-visualization-pro"
import audioAssets from "./assets/audio.mp3"

const App:React.FC<any> = ()=>{

  useEffect(()=>{
    createVisualization()
  })

  let AVP:any = null;
  /**初始化实例 */
  const createVisualization = ()=>{
    AVP = new (window as any).AudioVisualizationPro({
      url:audioAssets,
      loop:false
    })
  };
  /**播放&暂停 */
  const audioPlay = (type:number)=>{
    if(type){
      // currentTime . audiostate
      AVP.play(({frequency}:any)=>{
        createEcharts([...Array.from(frequency).reverse(), ...frequency])
      })
    }else{
      AVP.suspend()
    }
  };
  /*销毁实例*/
  const destruction = ()=>{
    if(AVP){
      AVP.destroy();
      AVP = null;
      createVisualization()
    }
  };
  /**实例化Echarts */
  const createEcharts = (data:any)=>{};


  return (
    <div className="app">
      <div className="titleContainer">音频可视化</div>
      <div className="echartsContainer" id="echartsContainer"></div>
      <div className="controllerContainer">
        <div onClick={()=>audioPlay(1)}>播放</div>
        <div onClick={()=>audioPlay(0)}>暂停</div>
        <div onClick={()=>destruction()}>销毁实例重新创建</div>
      </div>
    </div>
  )
}

export default App
```



