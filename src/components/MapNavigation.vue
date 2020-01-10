<template>
  <div class="hello">
    <span>
        地图导航，现在就是简单的地图位移（上下左右等）
    </span>
    <div>
        <button @click="moveToLeft">左移</button>
        <button @click="moveToRight">右移</button>
        <button @click="moveToUp">上移</button>
        <button @click="moveToDown">下移</button>
        <button @click="moveToChengDu">移到成都</button>
        <button @click="zoomIn">放大</button>
        <button @click="zoomOut">缩小</button>
    </div>
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
import { transform } from "ol/proj" //引入openlayer 坐标转换的
// import XYZ from 'ol/source/XYZ'  //加载地图的
import OSM from 'ol/source/OSM'  //加载地图的


export default {
  name: 'MapNavigation',
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
    // 向左移动地图
    moveToLeft() {
        var view = this.map.getView();
        var mapCenter = view.getCenter();
        // 让地图中心的x值增加，即可使得地图向左移动，增加的值根据效果可自由设定
        mapCenter[0] += 50000;
        view.setCenter(mapCenter);
        this.map.render();
    },

    // 向右移动地图
    moveToRight() {
        var view = this.map.getView();
        var mapCenter = view.getCenter();
        // 让地图中心的x值减少，即可使得地图向右移动，减少的值根据效果可自由设定
        mapCenter[0] -= 50000;
        view.setCenter(mapCenter);
        this.map.render();
    },

    // 向上移动地图
    moveToUp() {
        var view = this.map.getView();
        var mapCenter = view.getCenter();
        // 让地图中心的y值减少，即可使得地图向上移动，减少的值根据效果可自由设定
        mapCenter[1] -= 50000;
        view.setCenter(mapCenter);
        this.map.render();
    },

    // 向下移动地图
    moveToDown() {
        var view = this.map.getView();
        var mapCenter = view.getCenter();
        // 让地图中心的y值增加，即可使得地图向下移动，增加的值根据效果可自由设定
        mapCenter[1] += 50000;
        view.setCenter(mapCenter);
        this.map.render();
    },

    // 移动到成都
    moveToChengDu() {
        var view = this.map.getView();
        // 设置地图中心为成都的坐标，即可让地图移动到成都
        view.setCenter(transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'));
        this.map.render();
    },

    // 放大地图
    zoomIn() {
        var view = this.map.getView();
        // 让地图的zoom增加1，从而实现地图放大
        this.view.setZoom(view.getZoom() + 1);
    },

    // 缩小地图
    zoomOut() {
        var view = this.map.getView();
        // 让地图的zoom减小1，从而实现地图缩小
        view.setZoom(view.getZoom() - 1);
    },
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
