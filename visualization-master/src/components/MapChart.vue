<template>
  <div>
    <div id="map" class="w-full h-full p-2 rounded-lg"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { Map, View } from "ol";
import * as olProj from "ol/proj";
import TileLayer from "ol/layer/Tile";
import XYZ from "ol/source/XYZ";
import VectorSource from "ol/source/Vector";
import VectorLayer from "ol/layer/Vector";
import { Feature } from "ol";
import { Point } from "ol/geom";
import { Style, Circle, Fill } from "ol/style";

const openMap = ref(null);

const props = defineProps({
  data: {
    type: Object,
    required: true
  }
})

onMounted(() => {
  initMap();
});

function initMap() {
  openMap.value = new Map({
    target: "map",
    layers: [
      new TileLayer({
        source: new XYZ({
          url: "https://wprd0{1-4}.is.autonavi.com/appmaptile?lang=zh_cn&size=1&style=7&x={x}&y={y}&z={z}",
        }),
      }),
    ],
    view: new View({
      center: olProj.fromLonLat([104, 37]),
      zoom: 4,
      maxZoom: 18,
      minZoom: 3,
    }),
    controls: [],
  });

  // 创建多个点特征
  const points = [
    { lon: 104, lat: 37 },
    { lon: 116, lat: 39 },
    { lon: 120, lat: 30 }
  ];

  const pointFeatures = points.map(point => {
    return new Feature({
      geometry: new Point(olProj.fromLonLat([point.lon, point.lat])), // 点的坐标
    });
  });

  // 创建一个样式
  const pointStyle = new Style({
    image: new Circle({
      radius: 5,
      fill: new Fill({
        color: "red",
      }),
    }),
  });

  // 设置点的样式
  pointFeatures.forEach(feature => feature.setStyle(pointStyle));

  // 创建一个矢量图层源
  const vectorSource = new VectorSource({
    features: pointFeatures,
  });

  // 创建一个矢量图层
  const vectorLayer = new VectorLayer({
    source: vectorSource,
  });

  // 将矢量图层添加到地图中
  openMap.value.addLayer(vectorLayer);
}
</script>

<style scoped>
/* 你可以在这里添加一些样式 */
</style>
