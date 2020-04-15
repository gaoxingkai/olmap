// 基于StyleFeature的组件的。
<template>
  <div class="hello">
    <div id="map">
        <div>
            <el-switch
                @change="displayIcon(value1)"
                v-model="value1"
                inactive-text="显示图标">
            </el-switch>
        </div>
        <div class="deviceName" ref='tooltip' v-show="titleShow">
          <p>资产名称:{{name}}</p>
          <p>资产状态:{{status}}</p>
        </div>
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
import fixedPosition from "@/assets/icon_jian.png"

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data(){
    return {
        fixedPosition,
        map:null, //初始化地图1
        layers:[], //地图的layer
        view:null, //地图的view
        value1:true,
        pointList:[
            
        ],
        station_msg:[],
        VectorLayerIcon:null,
        name:'清风的手呀',
        status:'自己不去看看怎么会知道？',
        titleShow:false

      
    }
  },
  created () {
    for(let i = 0;i<=300;i++){
      let longitude = 110 + Math.random() * 6;
      let lat = 36.5 + Math.random() * 6;
      this.pointList.push([longitude, lat]);
    }
  },
  mounted(){
    this.mapInit(); //map初始化
  },
  methods:{
    displayIcon(item){
        if(item){
            this.VectorLayerIcon.setVisible(true)
        }else{
            this.VectorLayerIcon.setVisible(false)
        }
    },
    // map初始化
    mapInit(){
        // 初始化layers
        this.layers=[
            new TileLayer(
                {
                    source: new OSM(),
                    visible: true //指示该图层是否可见
                }
            ),
        ];
        // 初始化view
        this.view = new View({
            // 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
            // center: transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'),
            center:[114.06, 37.67],
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
        //添加point点
        this.pointList.forEach((item)=>{
            this.station_msg.push(
              new Feature({
                geometry: new Point(
                  item
                ),
                name:'我就是我颜色不一样的花火',
                status:"爱咋咋地",
              })
            );
        })
        // 我们需要一个vector的layer来放置图标
        this.VectorLayerIcon = new VectorLayer({
            source: new Vector({
                features: this.station_msg,
            }),
            style: new Style({
              image: new Icon({
                opacity: 0.95,
                src: fixedPosition,
                scale: 0.5,
                anchor:[0.5,1]
              }),
              cursor_: "pointer"
            })
        })
        this.map.addLayer(this.VectorLayerIcon);
        //根据图层的层级，放大或者缩小图标
        // 监听地图层级变化
        this.map.getView().on('change:resolution', function(){
            console.log('层级缩放了')
        })
        let _self = this;
        var obj = _self.$refs.tooltip;
        this.map.on("pointermove", function(evt) {  //这个是鼠标移动事件
            var pixel = _self.map.getEventPixel(evt.originalEvent);  //getEventPixel 返回浏览器事件相对于视口的地图像素位置
            console.log('pixel:',pixel);  //二维数组
            var hit = _self.map.hasFeatureAtPixel(pixel); //检测要素是否与视口上的像素相交
            console.log('hit:',hit); //boolean值，true or false
            if (hit) {
              _self.titleShow = true;

              _self.y = evt.pixel[1] - 40;
              _self.map.getTargetElement().style.cursor = "pointer";
              _self.map.forEachFeatureAtPixel(evt.pixel, function(feature) {
                console.log(feature)
                _self.name = feature.get("name");
                _self.status = feature.get("status");
                _self.x = evt.pixel[0] - (_self.name.length * 10) / 2;
                obj.style.left = _self.x - 100 + 'px'
                obj.style.top = _self.y + 20 + 'px'
                return false;
              });
            } else {
              _self.titleShow = false;
              _self.map.getTargetElement().style.cursor = "";
            }
          });
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #map{
    width: 1600px;
    height: 1600px;
  }
  .deviceName {
    width: 200px;
    height: 140px;
    background-color: rgba(0, 0, 0, .8);
    position: absolute;
    z-index: 2000;
    font-size: 12px;
    color: white;
    line-height: 30px;
    border-radius: 5px;
    padding: 20px;
    box-sizing: border-box;
  }
</style>
<style>
/* 地图刚开始不显示，查看高度为0，然后设置这个高度（不知道为啥以后在说） */
  #map1 .ol-viewport{ 
    height: 400px;
  }
   #map .el-switch__label.is-active {
    color: #333;
  }

  #map .el-switch__label {
    width: 230px;
    text-align: left;
  }
</style>
