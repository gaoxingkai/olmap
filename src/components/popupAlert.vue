<template>
  <div class="hello">
    <div id="map">

    </div>
    <div id="popup" class="ol-popup">
      <a href="#" id="popup-closer" @click.prevent="closerOnclick" class="ol-popup-closer"></a>
      <div id="popup-content" v-html="contentHtml"></div>
    </div>
  </div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map'; //init map
import View from 'ol/View'; //地图视图
import TileLayer from 'ol/layer/Tile'; //地图的图层
import { transform } from "ol/proj" //引入openlayer 坐标转换的
import OSM from 'ol/source/OSM'  //加载地图的

import Overlay from 'ol/Overlay';
import { toStringHDMS } from 'ol/coordinate';
import { toLonLat } from 'ol/proj';

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
      overlay:[],//
      contentHtml:''

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
        this.overlay = new Overlay({
            element: document.getElementById('popup'), //这里必须是一个dom元素
            autoPan: true,
            autoPanAnimation: {
                duration: 250
            }
        });
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
                overlays:[this.overlay],
                target: 'map'
            }
        );
        this.map.on('singleclick', (evt) => {  //地图的单击事件，地图事件的一个网址：https://blog.csdn.net/freeland1/article/details/50127427
            // 这里必须用箭头函数，否则this的指向会有问题，因为之前出现错误，所以记录一下
            var coordinate = evt.coordinate; //这个是3857坐标，可以用transform进行坐标转换(如果这个地图是以4326坐标系建立的那这个就是4326的坐标)
            console.log(evt.coordinate);
            let coor4326 = transform(coordinate, 'EPSG:3857', 'EPSG:4326')
            console.log('转换之后的坐标为：',coor4326); //[经度,纬度]
            var hdms = toStringHDMS(toLonLat(coordinate));//这个是转换为 30° 38′ 59″ N 103° 59′ 30″ E
            this.contentHtml = '<p>You clicked here:</p><code>' + hdms +
                '</code>';
            this.overlay.setPosition(coordinate); //设置overlayer的位置
        });
    },
    closerOnclick() {  //点击关闭的时候设置overlay的地理坐标为undefined
        this.overlay.setPosition(undefined);
        var closer = document.getElementById('popup-closer');
        closer.blur();
        return false;
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
  .map {
        width: 100%;
        height:400px;
      }
      .ol-popup {
        position: absolute;
        background-color: white;
        -webkit-filter: drop-shadow(0 1px 4px rgba(0,0,0,0.2));
        filter: drop-shadow(0 1px 4px rgba(0,0,0,0.2));
        padding: 15px;
        border-radius: 10px;
        border: 1px solid #cccccc;
        bottom: 12px;
        left: -50px;
        min-width: 280px;
      }
      .ol-popup:after, .ol-popup:before {
        top: 100%;
        border: solid transparent;
        content: " ";
        height: 0;
        width: 0;
        position: absolute;
        pointer-events: none;
      }
      .ol-popup:after {
        border-top-color: white;
        border-width: 10px;
        left: 48px;
        margin-left: -10px;
      }
      .ol-popup:before {
        border-top-color: #cccccc;
        border-width: 11px;
        left: 48px;
        margin-left: -11px;
      }
      .ol-popup-closer {
        text-decoration: none;
        position: absolute;
        top: 2px;
        right: 8px;
      }
      .ol-popup-closer:after {
        content: "✖";
      }
</style>
<style>
/* 地图刚开始不显示，查看高度为0，然后设置这个高度（不知道为啥以后在说） */
  #map1 .ol-viewport{
    height: 400px;
  }
</style>
