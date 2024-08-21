<template>
    <div class="flex items-center bg-slate-800">
      <div class="shadow-lg rounded-lg p-0 w-96 flex-1">
        <!-- 上部：数据总量 -->
        <div class="text-center mb-0">
          <div class="flex justify-center items-center">
            <h2 class="text-24 font-bold text-light-blue">数据总量:</h2>
            <p ref="totalCountup" class="text-5xl font-semibold ml-2 font-FX-LED text-gradient-blue">{{ data.total }}</p>
          </div>
        </div>
  
        <!-- 中部：各地的数据量 -->
        <div class="grid grid-cols-3 gap-1 mb-0">
          <div v-for="(value, key) in filteredData" :key="key" class="text-center">
            <div class="flex justify-center items-center">
              <p class="text-16 font-medium text-light-blue">{{ regionMap[key] }}:</p>
              <p :ref="el => regionCountup[key] = el" class="text-xl font-semibold ml-2 font-FX-LED text-sky-blue">{{ value }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { computed, ref, onMounted } from 'vue';
  import { CountUp } from 'countup.js'; // 确保正确导入 countup 库
  
  const props = defineProps({
    data: {
      type: Object,
      required: true
    }
  });
  
  const regionMap = {
    hb: '华北',
    db: '东北',
    zn: '中南',
    xn: '西南',
    xb: '西北',
    hd: '华东'
  };
  
  const filteredData = computed(() => {
    return Object.keys(props.data).reduce((acc, key) => {
      if (key !== 'total') {
        acc[key] = props.data[key];
      }
      return acc;
    }, {});
  });
  
  const totalCountup = ref(null);
  const regionCountup = ref({});
  
  onMounted(() => {
    if (totalCountup.value) {
      new CountUp(totalCountup.value, props.data.total).start();
    }
  
    Object.keys(filteredData.value).forEach((key) => {
      if (regionCountup.value[key]) {
        new CountUp(regionCountup.value[key], filteredData.value[key]).start();
      }
    });
  });
  </script>
  
