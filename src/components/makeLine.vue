<template>
  <div class="hello">
    <div id="map">

    </div>
    <div @click="removeInteraction">
        points:{{ points }}
    </div>
  </div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map'; //init map
import View from 'ol/View'; //地图视图
import TileLayer from 'ol/layer/Tile'; //地图的图层
import { transform } from "ol/proj" //引入openlayer 坐标转换的
// import OSM from 'ol/source/OSM'  //加载地图的
import XYZ from 'ol/source/XYZ';
import Draw from 'ol/interaction/Draw'; //互动绘图
import VectorLayer from 'ol/layer/Vector'
import {Vector} from 'ol/source';
import Style from 'ol/style/Style';
import Stroke from 'ol/style/Stroke';

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data(){
    return {
      map:null, //初始化地图1
      layers:[], //地图的layer
      view:null, //地图的view
      points:null,
      lineDraw:null,
      lineLayer:null,
    }
  },
  created () {
    
  },
  mounted(){
    this.mapInit(); //map初始化
  },
  methods:{
    // map初始化
    mapInit(){
       // 初始化layers
        this.layers.push(
            new TileLayer(
                {
                    source: new XYZ({
                      url:'http://mt2.google.cn/vt/lyrs=y&hl=zh-CN&gl=CN&src=app&x={x}&y={y}&z={z}&s=G'
                    }),
                    visible: true //指示该图层是否可见
                }
            )
        );
        // 初始化view
        this.view = new View({
            // 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
            center: transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'),
            // projection: 'EPSG:4326',
            zoom: 10
        })
      this.map = new Map(
        {
            // logo: false, //设置地图logo不显示
            // logo: {src: '../img/face_monkey.png', href: 'http://www.openstreetmap.org/'},  //logo没测试
            layers: this.layers,
            view: this.view,
            target: 'map'
        }
      );
      // 添加一个绘制的线使用的layer
        this.lineLayer = new VectorLayer({
            source: new Vector(),
            style: new Style({
                stroke: new Stroke({
                    color: 'red',
                    size: 1
                })
            })
        })
        this.map.addLayer(this.lineLayer);
        // 添加绘图的交互类
        this.lineDraw = new Draw({
            type: 'LineString', // 设置绘制线
            source: this.lineLayer.getSource() ,
            style: new Style({            // 设置绘制时的样式
                stroke: new Stroke({
                    color: '#009933',
                    size: 3
                })
            }),
            maxPoints: 4    // 限制不超过4个点
        })
        // 监听线绘制结束事件，获取坐标
        this.lineDraw .on('drawend', event => {
            // event.feature 就是当前绘制完成的线的Feature
            this.points = JSON.stringify(event.feature.getGeometry().getCoordinates());
        });

        this.map.addInteraction(this.lineDraw );
      
    },
    // 取消绘制
    removeInteraction(){ //可以清除掉，但是清除了之后就不能再次绘制了
        // console.log('dianji')
        // this.lineLayer.getSource().clear();
        // this.map.removeInteraction(this.lineDraw)
        this.lineLayer.setVisible(false)  //设置这个图层隐藏
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #map{
    width: 400px;
    height: 400px;
  }
</style>
<style>
/* 地图刚开始不显示，查看高度为0，然后设置这个高度（不知道为啥以后在说） */
  #map1 .ol-viewport{ 
    height: 400px;
  }
</style>
