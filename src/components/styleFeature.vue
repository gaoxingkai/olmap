<template>
  <div class="hello">
    <div id="map">

    </div>
  </div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map'; //init map
import View from 'ol/View'; //地图视图
import TileLayer from 'ol/layer/Tile'; //地图的图层
// import { transform } from "ol/proj" //引入openlayer 坐标转换的
import OSM from 'ol/source/OSM'  //加载地图的
import VectorLayer from 'ol/layer/Vector'
import {Vector} from 'ol/source';
import Feature from 'ol/Feature';
import Style from 'ol/style/Style';
import Icon from 'ol/style/Icon';
import Point from 'ol/geom/Point';
import fixedPosition from "@/assets/fixedPosition.png"

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data(){
    return {
        fixedPosition:fixedPosition,
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
        // 我们需要一个vector的layer来放置图标
        let VectorLayerIcon = new VectorLayer({
            source: new Vector()
        })
       // 初始化layers
        this.layers=[
            new TileLayer(
                {
                    source: new OSM(),
                    visible: true //指示该图层是否可见
                }
            ),
            VectorLayerIcon
        ];
        
        // 初始化view
        this.view = new View({
            // 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
            // center: transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'),
            center:[104.06, 30.67],
            projection:'EPSG:4326',
            zoom: 8
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
    
        // 创建一个Feature，并设置好在地图上的位置
        var anchor = new Feature({
            geometry: new Point([104.06, 30.67])
        });
        // 设置样式，在样式中就可以设置图标
        anchor.setStyle(new Style({
            image: new Icon({
                src: this.fixedPosition,
                anchor: [0.5, 1], //设置坐标为icon的底部。为什么是[0.5, 1]这种值，表示什么？ 默认情况下，
                // 位置坐标是按照比例的方式来设置的，范围从0到1，x轴上0表示最左边，
                // 1表示最右边，y轴上0表示最上边，1表示最下边。 如代码所示，x设置为0.5可以让图片在x方向上居中，
                // y设置为1可以让图片在y方向上移动到最底端。
                // size:[30,30], //设置icon的像素尺寸，单位：像素
            }),
        }));
        // 添加到之前的创建的layer中去
        VectorLayerIcon.getSource().addFeature(anchor);

            //根据图层的层级，放大或者缩小图标
        // 监听地图层级变化
        this.map.getView().on('change:resolution', function(){
            var style = anchor.getStyle();
            // 重新设置图标的缩放率，基于层级10来做缩放
            style.getImage().setScale(this.getZoom() / 10);
            anchor.setStyle(style);
        })
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #map{
    width: 600px;
    height: 600px;
  }
</style>
<style>
/* 地图刚开始不显示，查看高度为0，然后设置这个高度（不知道为啥以后在说） */
  #map1 .ol-viewport{ 
    height: 400px;
  }
</style>
