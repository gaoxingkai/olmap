<template>
  <div class="hello">
    <div id="map">

    </div>
  </div>
</template>

<script>
import 'ol/ol.css';
import Map from 'ol/Map';
import View from 'ol/View';
import {defaults as defaultControls, ScaleLine} from 'ol/control';
import TileLayer from 'ol/layer/Tile';
import XYZ from 'ol/source/XYZ'

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data(){
    return {
      map:null,
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
      // 地图的本质是一个一个的图层类似ps
      var layers = [
        // 新建一个地图图层
        new TileLayer({
            source: new XYZ({ //加载地图的路径url
              url: "http://www.google.cn/maps/vt?lyrs=s@189&gl=cn&x={x}&y={y}&z={z}"
            }),
            visible: true //指示该图层是否可见
          })
      ];

      this.map = new Map({
        controls: defaultControls().extend([
          new ScaleLine({
            units: 'degrees'
          })
        ]),
        layers: layers,
        target: 'map',
        view: new View({
          projection: 'EPSG:4326', //使用这个坐标系
          center: [103.9323, 30.67], // 地图中心点的经纬度[经度，纬度]当前的这个经纬度为成都
          zoom: 12 //当前地图的放大层级为12级别
        })
      });
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #map{
    width:100%;
    height:100%;
    min-width: 200px;
    min-height: 600px;
  }
</style>
<style>
  #map .ol-viewport{
    min-height: 600px;
  }
</style>
