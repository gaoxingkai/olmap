<template>
  <div id="app">
    <div class="buttonDiv">
      <button :class="current === item.componentsName?'activeButton':''" @click="tabMap(item)" v-for="(item,index) in mapList" :key="index">{{ item.mapName }}</button>
    </div>
    <div :is="current"></div>
  </div>
</template>

<script>
import hellworld from './components/HelloWorld.vue'
import mapnavigation from './components/MapNavigation.vue'
import olmap from './components/map.vue'
import restrictedmap from './components/restrictedmap.vue'
import mapswitching from './components/mapswitching.vue'
import iconAndTips from './components/iconAndTips'
import styleFeature from './components/styleFeature'
import makeLine from './components/makeLine'
import hotHeat from './components/hotHeat'
import olcontrol from './components/control'

export default {
  name: 'app',
  components: {
    hellworld,
    mapnavigation,
    olmap,
    restrictedmap,
    mapswitching,
    iconAndTips,
    styleFeature,
    makeLine,
    hotHeat,
    olcontrol
  },
  data(){
    return {
      current:'hellworld', // 切换显示组件
      mapList:[
        {
          mapName:'原始地图',
          componentsName:'olmap'
        },
        {
          mapName:'地图初始化',
          componentsName:'hellworld'
        },
        {
          mapName:'地图导航',
          componentsName:'mapnavigation'
        },
        {
          mapName:'限制地图范围，缩放层级，自适应区域',
          componentsName:'restrictedmap'
        },
        {
          mapName:'地图切换',
          componentsName:'mapswitching'
        },
        {
          mapName:'地图icon1',
          componentsName:'iconAndTips'
        },
        {
          mapName:'地图icon2---style+feature',
          componentsName:'styleFeature'
        },
        {
          mapName:'绘制一条线',
          componentsName:'makeLine'
        },
        {
          mapName:'热力图',
          componentsName:'hotHeat'
        },
        {
          mapName:'控件',
          componentsName:'olcontrol'
        },

        
      ]
    }
  },
  methods:{
    /*
      1. OpenLayers 3默认使用的是EPSG:3857
    */
   tabMap(item){
     this.current = item.componentsName;
   },
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.activeButton{
  background:#2c3e50;
  color:#fff;
}
button{
  outline:none;
}
.buttonDiv{
  padding:10px;
}
.buttonDiv button{
  margin: 0 20px;
}
</style>
