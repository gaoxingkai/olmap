<template>
  <div class="hello">
    <input type="radio" checked="checked" name="mapSource" @click="switch2OSM" />OpenStreetMap地图
    <input type="radio" name="mapSource" @click="switch2BingMap" />Bing地图
    <div id="map">

    </div>
  </div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map'; //init map
import View from 'ol/View'; //地图视图
import TileLayer from 'ol/layer/Tile'; //地图的图层
import { transform } from "ol/proj" //引入openlayer 坐标转换的
import OSM from 'ol/source/OSM'  //加载地图OSM
import XYZ from 'ol/source/XYZ'  //加载地图的
// import BingMaps from 'ol/source/BingMaps'  //加载地图BingMaps 

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
      bingMapLayer:null,
      openStreetMapLayer:null,
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

        // 这个是一个地图底图
        this.bingMapLayer = new TileLayer({
            // source: new BingMaps({
            //     key: 'AkjzA7OhS4MIBjutL21bkAop7dc41HSE0CNTR5c6HJy8JKc7U9U9RveWJrylD3XJ',
            //     imagerySet: 'Road'
            // })
            source: new XYZ({
                url: 'http://{a-c}.tile.openstreetmap.org/{z}/{x}/{y}.png'
            })
        });
        // 这个是一个地图底图
        this.openStreetMapLayer = new TileLayer({
            source: new OSM()
        });
       // 初始化layers
        this.layers.push(
            new TileLayer(
                {
                    source: new OSM(),
                    visible: true //指示该图层是否可见
                }
            )
        );
        // 初始化view
        this.view = new View({
            // 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
            center: transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'),
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
    },
    // 地图切换
    switch2BingMap() {
      // 先移除当前的地图，再添加Bing地图
      this.map.removeLayer(this.map.getLayers().item(0));
      this.map.addLayer(this.bingMapLayer);
    },
    switch2OSM() {
      // 先移除当前的地图，再添加Open Street Map 地图
      this.map.removeLayer(this.map.getLayers().item(0));
      this.map.addLayer(this.openStreetMapLayer);
    }
  }
}
</script>
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
