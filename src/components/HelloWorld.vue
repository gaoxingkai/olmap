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
import {defaults as defaultControls, ScaleLine} from 'ol/control';
import TileLayer from 'ol/layer/Tile'; //地图的图层
import XYZ from 'ol/source/XYZ'  //加载地图的

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
      /*  
        *  Layer的主要属性：
        *  opacity，不透明度，默认为 1 ，即完全不透明；
        *  zIndex，图层的叠放次序，默认为0，值最小的图层位于最下方；
        *  visible，是否可见，默认为 true ；
        *  extent，图层渲染的区域，格式为[minx,miny,maxx,maxy]。如果没有设置该参数，图层就不会显示；
        *  minResolution，最小分辨率，当图层的缩放级别小于这个分辨率时，图层就会隐藏；
        *  maxResolution，最大分辨率，当图层的缩放级别等于或超过这个分辨率时，图层就会隐藏；
        *  source，图层的来源。
        *
        */
          // Layer的主要方法：
          // getLayersArray()，得到所有图层；
          // getLayerStatesArray()，得到所有图层状态；
          // getSource()，得到相应图层的来源；
          // getSourceState()，得到相应图层的来源状态；
          // setSource()，设置图层 source 属性，参数为一个 source 对象；
          // setMap()，添加Layer到Map,并由Map管理。



      // 地图的本质是一个一个的图层类似ps
      this.layers = [
        // 新建一个地图图层
        new TileLayer({
            source: new XYZ({ //加载地图的路径url
              url: "http://www.google.cn/maps/vt?lyrs=s@189&gl=cn&x={x}&y={y}&z={z}"  //定义了一个图层,数据来源是url的地址。
              // url:'http://192.168.1.203:8080/geoserver/gwc/service/wmts',
            }),
            visible: true //指示该图层是否可见
          })
      ];
        // Vector/VectorLayer
        // Vector指矢量数据，VectorLayer自己定义了很多属性和方法：
        // renderOrder：渲染地理要素时的顺序，一般情况下，在渲染之前，要素是基于一定规则排序的，而渲染就是根据这个顺序进行依次渲染的，这个参数便指定了这个排序规则，如果赋值为 null ，那么就不会对地理要素进行排序，渲染也不会有一定的顺序；
        // renderBuffer：默认值为100；
        // style：规定了矢量图层的样式，即配色、形状等；
        // renderMode：渲染模式，Image表示数据被渲染为图像，点和文本信息会随图像旋转，缩放时像素也会被缩放；Vector表示将数据渲染为向量，最精确但性能较慢。
        // updateWhileAnimating：当有动画特效时，地理要素是否被重新创建，默认是 false；
        // updateWhileInteracting：当地理要素交互时，是否重新渲染

        // 地图的view
      this.view = new View({
        projection: 'EPSG:4326', //使用这个坐标系
        center: [114.38,37.67],    // 地图中心点的经纬度[经度，纬度]当前的这个经纬度为成都
        zoom: 12,    //当前地图的放大层级为12级别
      })
      this.map = new Map({
        controls: defaultControls().extend([
          new ScaleLine({
            units: 'degrees'
          })
        ]),
        layers: this.layers,
        target: 'map', //target:'map'指定了地图要显示在id为map的div中
        view: this.view
      });
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
