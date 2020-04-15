<template>
  <div class="hello">
    <div id="map">
        <button @click="optical_LayerShow">切换显示</button>
        <button @click="consoleLayer">打印图层，并隐藏layerId为layer1的图层。</button>
        <div v-if="false">
          关于如何获取图层问题，简单来说就是我想要那个图层，我就能根据某一个属性获取到这个图层的实现思路，
          如何获取指定属性的图层
          layer继承了Object类，所以有set和get方法。可以在加载图层时，通过相应的layer.set(‘name’,‘name’)；
          在获取图层时，遍历所有获取到的layer，并用layer.get(‘name’)即可获取到相应的图层。
          记录的一个博客地址：https://blog.csdn.net/qq_29602347/article/details/100877952
          提示：在打印的图层对象上并不能找到自己设置的相关的属性，需要通过get方法获取。
          获取地图图层的方式和相关逻辑直接看代码逻辑。
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
      opticalShow:true,
    }
  },
  created () {
      let company = this.getDistance(103.99448599265008,30.66199163525242,103.89448599265008, 30.34534513525242)
      console.log(company);
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
      consoleLayer(){
        console.log("打印图层click")
        console.log(this.map.getLayers().getArray());  // 打印获取到的map图层。
        /**
         * 在获取图层时，遍历所有获取到的layer，并用layer.get(‘name’)即可获取到相应的图层。
         *
         *
         *
         */
        let thisMapLayers = this.map.getLayers().getArray(); // 获取到当前地图的所有图层。
        thisMapLayers.map(v => {
          console.log(v.get("layerId"));
          /****
           *
           * 打印图层的时候如果当前的图层是线段的图层的话，隐藏图层。
           *
           *
           * */
          if(v.get("layerId") === "layer1"){
            v.setVisible(false); // 隐藏当前图层。
            this.opticalShow = false;
            console.log("当前图层是线段图层");
          }
        })
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
          this.optical_Layer.set("layerId","layer1");
       // 初始化layers
        let this_layers = new TileLayer(
                {
                    source: new OSM(),
                    visible: true //指示该图层是否可见
                }
            )
            this_layers.set("layerId","layer2"); //
/**
 *
 * layer继承了Object类，所以有set和get方法。可以在加载图层时，通过相应的layer.set(‘name’,‘name’)；
 * 在获取图层时，遍历所有获取到的layer，并用layer.get(‘name’)即可获取到相应的图层。
 * 在consoleLayer事件中获取当前的layer图层；
 *
 */

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
        // 地图的单击事件
          /*
          *
          * 点击地图获取到当前点击的位置坐标，并划线，最后计算点击过的点之间的距离！
          *
          * **/
          this.map.on('singleclick', (evt) => {  //地图的单击事件，地图事件的一个网址：https://blog.csdn.net/freeland1/article/details/50127427
              // 这里必须用箭头函数，否则this的指向会有问题，因为之前出现错误，所以记录一下
              console.log(evt.coordinate); //打印当前的位置坐标
          });
        /***
         * 计算距离结束
         *
         * ***/
    },
      // 根据经纬度算出两个坐标值之间的距离
      getDistance(lon1, lat1, lon2, lat2,){
          let radLat1 = lat1*Math.PI / 180.0;
          let radLat2 = lat2*Math.PI / 180.0;
          let a = radLat1 - radLat2;
          let b = lon1*Math.PI / 180.0 - lon2*Math.PI / 180.0;
          let s = 2 * Math.asin(Math.sqrt(Math.pow(Math.sin(a/2),2) +
              Math.cos(radLat1)*Math.cos(radLat2)*Math.pow(Math.sin(b/2),2)));
          s = s *6378.137 ;// EARTH_RADIUS;
          s = Math.round(s * 10000) / 10000;
          return s;
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
