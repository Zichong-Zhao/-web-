<template>
    <div>
      <div>【大区数据信息】</div>
      <div ref="RadarBar" class="w-full h-full"></div>
    </div>
  </template>
  
  <script setup>
  import { onMounted, ref, watch, defineProps } from 'vue';
  import * as echarts from 'echarts';
  
  const props = defineProps({
    data: {
      type: Object,
      required: true
    }
  });
  
  const RadarBar = ref(null);
  let chart = null;
  
  onMounted(() => {
    if (RadarBar.value) {
      try {
        chart = echarts.init(RadarBar.value);
        chart.setOption(option);
      } catch (error) {
        console.error('Failed to initialize echarts:', error);
      }
    }
  });
  
  watch(
    () => props.data,
    (newData) => {
      const formattedData = attackSourcesDataFmt(newData.regions);
      chart.setOption({
        series: [
          {
            data: formattedData
          }
        ]
      });
    }
  );
  
  // 把16进制的颜色改成rgba格式
  const hexToRgba = (hex, alpha) => {
    try {
      hex = hex.replace(/^#/, '');
  
      if (hex.length === 3) {
        hex = hex.split('').map(char => char + char).join('');
      }
  
      if (hex.length !== 6) {
        throw new Error('Invalid HEX color.');
      }
  
      const r = parseInt(hex.substring(0, 2), 16);
      const g = parseInt(hex.substring(2, 4), 16);
      const b = parseInt(hex.substring(4, 6), 16);
  
      return `rgba(${r}, ${g}, ${b}, ${alpha})`;
    } catch (error) {
      console.error('Failed to convert hex to rgba:', error);
      return 'rgba(0, 0, 0, 0)'; // 返回透明色作为默认值
    }
  };
  
  const attackSourcesColor1 = ['#DFBB71', '#DADADA', '#C46026', '#D5D5D5', '#F76F1C', '#EC6D6D'];
  
  // 拼接itemStyle
  const attackSourcesDataFmt = (sData) => {
    return sData.map((item, i) => {
      if (i < attackSourcesColor1.length) {
        return {
          value: item.value,
          itemStyle: {
            borderRadius: [0, 0, 0, 0],
            color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [
              { offset: 0, color: hexToRgba(attackSourcesColor1[i], 0.6) },
              { offset: 1, color: attackSourcesColor1[i] }
            ])
          }
        };
      }
      return null;
    }).filter(item => item !== null);
  };
  
  const option = {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'axis',
      backgroundColor: "RGBA(0, 49, 85, 1)",
      borderColor: "rgba(0, 151, 251, 1)",
      formatter: ' {b}     {c}',
      axisPointer: { type: 'shadow' },
      textStyle: {
                color: "#BCE9FC",
                fontSize: 14,
                align: "left",
            },
    },
    grid: {
      top: '5%',
      left: '5%',
      bottom: '5%',
      right: '5%',
      containLabel: true,
      backgroundColor: 'transparent'
    },
    xAxis: {
      axisLine: { lineStyle: { color: '#44A0B7' } },
      axisLabel: { color: '#fff' },
      show: false,
      type: 'value',
      axisTick: { show: false },
      splitLine: { show: false }
    },
    yAxis: {
      type: 'category',
      inverse: true,
      data: props.data?.regions?.map(item => item.name) || [],
      axisLine: { show: false, lineStyle: { color: '#44A0B7' } },
      axisLabel: {
        show: true,
        margin: 15,
        color: 'rgb(188,217,230)',
        fontSize: 14,
        rotate: 0,
        verticalAlign: 'middle',
        align: 'right',
        padding: [-20, 0, 15, 20]
      },
      axisTick: { show: false },
      splitLine: { show: false }
    },
    series: [
      {
        type: 'bar',
        barWidth: '15%',
        data: attackSourcesDataFmt(props.data?.regions || []),
        label: {
          show: true,
          formatter: '{c}',
          lineHeight: 10,
          offset: [30, 0],
          position: 'insideRight',
          color: '#FFFFFF',
          fontSize: 12,
          fontWeight: 600
        },
      },
      {
        zlevel: 1,
        name: '背景',
        type: 'bar',
        barWidth: '30%',
        barGap: '-160%',
        data: Array(6).fill(100),
        itemStyle: {
          normal: {
            color: 'rgba(131, 132, 132, 0)',
            barBorderRadius: [0, 0, 0, 0],
            borderColor: 'rgba(160, 160, 160,0.5)'
          }
        }
      }
    ]
  };
  </script>
  