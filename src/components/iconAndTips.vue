<template>
  <div class="hello">
    <div id="map">

    </div>
    <div id="iconImage">
        <img :src="fixedPosition" alt="示例锚点">
    </div>
  </div>
</template>

<script>
import 'ol/ol.css';
import fixedPosition from "@/assets/fixedPosition.png"
import Map from 'ol/Map'; //init map
import View from 'ol/View'; //地图视图
import TileLayer from 'ol/layer/Tile'; //地图的图层
import { transform } from "ol/proj" //引入openlayer 坐标转换的
import OSM from 'ol/source/OSM'  //加载地图的
import Overlay from 'ol/Overlay'; //添加定位图标

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data(){
    return {
        fixedPosition:fixedPosition, //定位地图的图标
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
        // 添加图标
        var anchor = new Overlay({
            element: document.getElementById('iconImage')
        });
        // 关键的一点，需要设置附加到地图上的位置
        anchor.setPosition(transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857')); //图标的定位位置和理想的不一致，暂时先不研究这个；
        //不一致的原因是因为之前没有设置ol的坐标系，那样就会是3857的坐标系，经过敬畏杜的转换之后图标成功定位 到成都，不多在进行地图缩放的时候地理位置会发生偏移
        // 然后添加到map上
        this.map.addOverlay(anchor);
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
  #iconImage img{
      width:50px;
      height:50px;
  }
</style>
<style>
/* 地图刚开始不显示，查看高度为0，然后设置这个高度（不知道为啥以后在说） */
  #map1 .ol-viewport{ 
    height: 400px;
  }
</style>
