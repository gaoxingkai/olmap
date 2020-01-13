<template>
  <div class="hello">
      <button @click="fitToChengdu">显示成都自适应区域</button>
    <div id="map">

    </div>
  </div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map'; //init map
import View from 'ol/View'; //地图视图
// import {defaults as defaultControls, ScaleLine} from 'ol/control';
import TileLayer from 'ol/layer/Tile'; //地图的图层
// import XYZ from 'ol/source/XYZ'  //加载地图的
// import { transform } from "ol/proj" //引入openlayer 坐标转换的
import OSM from 'ol/source/OSM'  //加载地图的

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
                    source: new OSM(),
                    visible: true //指示该图层是否可见
                }
            )
        );
        // 初始化view
        this.view = new View({
            // 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
            // center: transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'), //使用这句会使fitToChengdu方法打不成自己想要的
            center: [104.06, 30.67],
            projection: 'EPSG:4326', //（默认为'EPSG：3857'）这里设置为4326
            zoom: 10
        })
        this.map = new Map(
            {
                // logo: false, //设置地图logo不显示
                // logo: {src: '../img/face_monkey.png', href: 'http://www.openstreetmap.org/'},  //logo没测试
                layers: this.layers,
                view: this.view, //view视图
                extent: [102, 29, 104, 31], //限制地图区域【经度，纬度，经度，纬度】
                zoom: 10, //限制地图默认的缩放等级
                minZoom: 10, //最小级别为10
                maxZoom: 14, //限制地图缩放最大级别为14
                target: 'map'
            }
        );
    },
    //自己测试不可以，但是案例可以，案例地址http://anzhihun.coding.me/ol3-primer/ch04/04-08.html
    fitToChengdu() {
        // 让地图最大化完全地显示区域[104, 30.6, 104.12, 30.74]
        this.map.getView().fit([104, 30.6, 104.12, 30.74], this.map.getSize());
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
