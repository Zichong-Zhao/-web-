<template>
<div 
  class="bg-[url('assets/imgs/bg.jpg')] bg-cover bg-center bg-no-repeat h-screen text-white p-3 flex overflow-hidden"
  v-if="data">
  <div class="flex-1 mr-3 bg-opacity-75 bg-slate-800 rounded-lg p-3 flex flex-col ">
    <!-- // 横向柱状图 -->
    <HorizontalBar class="h-1/3 box-border pb-4" :data="data.regionData" />
    <!-- // 雷达图 -->
    <RadarBar class="h-1/3 box-border pb-4" :data="data.riskData" />
    <!-- //关系图 -->
    <Relation class="h-1/3 box-border pb-4" :data="data.relationData"/>
  </div>
  <div class="w-1/2  mr-3 flex flex-col ">
    <!-- // 总数据 -->
    <TotalData class="bg-opacity-50 bg-slate-800 p-1 rounded-lg" :data="data.totalData" />
    <!-- // 地图 -->
    <MapChart class="bg-opacity-50 bg-slate-800 p-1 mt-1 flex-1 rounded-lg" :data="data.mapData" />
  </div>
  <div class="flex-1 bg-opacity-75 bg-slate-800 rounded-lg p-3 flex flex-col ">
    <!-- //竖向柱状图 -->
    <VerticalBar class="h-1/3 box-border pb-4" :data="data.serverData" />
    <!-- // 环形图 -->
    <RingBar class="h-1/3 box-border pb-4" :data="data.abnormalData" />
    <!-- // 文档云图 -->
    <WorldCloud class="h-1/3 box-border pb-4" :data="data.wordCloudData" />
  </div>
</div>
</template>


<script setup>
import HorizontalBar from './components/HorizontalBar.vue';
import VerticalBar from './components/VerticalBar.vue';
import Relation from './components/Relation.vue';
import TotalData from './components/TotalData.vue';
import MapChart from './components/MapChart.vue';
import RingBar from './components/RingBar.vue';
import WorldCloud from './components/WorldCloud.vue';
import RadarBar from './components/RadarBar.vue';

import { ref } from 'vue';
import { getVisualization } from './api/visualization.js';
import { registerAction } from 'echarts';

const data = ref(null);
const loadData = async () => {
  data.value = await getVisualization();
  console.log(data.value);
};

loadData();
setInterval(() => {
  loadData();
}, 3000);
</script>
<style lang="scss" scoped></style>