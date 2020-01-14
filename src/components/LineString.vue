<template>
  <div class="hello">
    <div id="map">
        <button @click="optical_LayerShow">切换显示</button>
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
import { LineString } from "ol/geom";
import Feature from 'ol/Feature';
import VectorLayer from 'ol/layer/Vector'
import {Vector} from 'ol/source';
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
      optical_Layer:null, //线段图层
      pointList:[
        [103.99448599265008, 30.66199163525242],
        [103.89448599265008, 30.34534513525242],
        [103.74238599265008, 30.34534535325242],
        [103.63423235665008, 30.66134534135242],
        [103.52131239265008, 30.66934791237242],
        [103.43432499265008, 30.68721368225242],
        [103.34534599265008, 30.61822105239542],
      ],
      opticalShow:false,
    }
  },
  created () {
    
  },
  mounted(){
    this.mapInit(); //map初始化
  },
  methods:{
      optical_LayerShow(){
            this.opticalShow = !this.opticalShow;
            console.log(this.opticalShow)
            if(this.opticalShow){
                this.optical_Layer.setVisible(true);
            }else this.optical_Layer.setVisible(false);
            
      },
    // map初始化
    mapInit(){
        // 线段初始化
          let optical_trackLine = new LineString([...this.pointList]);
          var optical_lineStyle = new Feature({
            geometry: optical_trackLine
          })
          this.optical_Layer = new VectorLayer({
            source: new Vector({
              features: [
                optical_lineStyle
              ]
            }),
            visible: true
          });

       // 初始化layers
        let this_layers = new TileLayer(
                {
                    source: new OSM(),
                    visible: true //指示该图层是否可见
                }
            )
        // 初始化view
        this.view = new View({
            // 设置成都为地图中心，此处进行坐标转换， 把EPSG:4326的坐标，转换为EPSG:3857坐标，因为ol默认使用的是EPSG:3857坐标
            // center: transform([104.06, 30.67], 'EPSG:4326', 'EPSG:3857'),
            center: [104.06, 30.67],
            zoom: 10,
            projection:'EPSG:4326' //这个属性是指将ol的坐标用EPSG:4326的，若不指定，ol默认是EPSG:3857的,center的中心坐标如果是4326的就需要转化
        })

      this.map = new Map(
        {
            // logo: false, //设置地图logo不显示
            // logo: {src: '../img/face_monkey.png', href: 'http://www.openstreetmap.org/'},  //logo没测试
            layers: [this_layers,this.optical_Layer],
            view: this.view,
            target: 'map'
        }
      );
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
