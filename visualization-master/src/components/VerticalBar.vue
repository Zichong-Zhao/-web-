<template>
	<div>
		<div>【服务资源占用比】</div>
		<div ref="target" class="w-full h-full"></div>
	</div>
</template>

<script setup>
import { onMounted, onUnmounted, ref, watch } from 'vue'
import * as echarts from 'echarts'

const props = defineProps({
	data: {
		type: Object,
		required: true
	}
})

const target = ref(null)
let mChart = null

onMounted(() => {
	if (target.value && props.data) {
		mChart = echarts.init(target.value)
		renderChart()
	}
})

onUnmounted(() => {
	if (mChart) {
		mChart.dispose()
	}
})

const renderChart = () => {
    if (!mChart) return; // 增加错误处理，防止mChart未初始化

    const path = "image:////img.isqqw.com/profile/upload/2023/11/15/fabee49f-3e3b-45a6-a5d5-2780f5684bde.png";

    const option = {
        backgroundColor: "transparent", // 将背景改为透明
        tooltip: {
            show: true,
            trigger: "axis",
            backgroundColor: "RGBA(0, 49, 85, 1)",
            borderColor: "rgba(0, 151, 251, 1)",
            borderWidth: 1,
            borderRadius: 0,
            textStyle: {
                color: "#BCE9FC",
                fontSize: 14,
                align: "left",
            },
            formatter: (params) => {
                if (params.length > 0) {
                    return "百分比：" + params[0].data.toString()+"%";
                }
                return "";
            },
        },
        grid: {
            show: false,
            left: "2%",
            right: "2%",
            bottom: "5%",
            top: "5%",
            containLabel: true,
        },
        xAxis: {
            type: "category",
            boundaryGap: true,
            data: props.data.servers.map((item) => item.name),
            axisLabel: {
                interval: 0,
                textStyle: {
                    color: "#fff",
                    fontSize: 12,
                },
            },
            axisTick: {
                show: false,
            },
            axisLine: {
                lineStyle: {
                    color: "rgba(77, 128, 254, 0.2)",
                },
            },
            splitLine: {
                show: true,
                lineStyle: {
                    color: "rgba(77, 128, 254, 0.2)",
                },
            },
        },
        yAxis: {
            name: "百分比",
            nameTextStyle: {
                color: "#A8B1BB",
                fontSize: 12,
                padding: [10, 50, 0, 10],
            },
            type: "value",
            max: 100,
            interval: 20,
            splitNumber: 3,
            axisLabel: {
                show: false, // 去掉y轴标签
            },
            axisLine: {
                show: false,
            },
            axisTick: {
                show: false,
            },
            splitLine: {
                show: true,
                lineStyle: {
                    color: "rgba(77, 128, 254, 0.2)",
                },
            },
        },
        series: [
            {
                name: "百分比",
                type: "pictorialBar",
                barWidth: "60%",
                stack: "总量",
                label: {
                    show: true,
				// 设置标签位置为右侧
				position: 'top',
				textStyle: {
					// 设置标签文本颜色
					color: '#fff'
				},
				formatter: '{c}%'
                },
                itemStyle: {
                    color: {
                        type: "linear",
                        x: 0,
                        y: 0,
                        x2: 0,
                        y2: 1,
                        colorStops: [
                            { offset: 0, color: "rgba(0, 151, 251, 1)" },
                            { offset: 1, color: "rgba(0, 151, 251, 0)" },
                        ],
                        globalCoord: false,
                    },
                },
                symbol: "path://M12.000,-0.000 C12.000,-0.000 16.074,60.121 22.731,60.121 C26.173,60.121 -3.234,60.121 0.511,60.121 C7.072,60.121 12.000,-0.000 12.000,-0.000 Z",
                data: props.data.servers.map((item) => item.value),
            },
            {
                type: "line",
                symbolOffset: [1, 3],
                lineStyle: {
                    color: "transparent",
                },
                barCategoryGap: "0%",
                symbol: path,
                symbolSize: 15,
                data: props.data.servers.map((item) => item.value),
                z: 10,
            },
        ],
    };

    mChart.setOption(option);
}

watch(() => props.data, () => {
    if (mChart) { // 增加错误处理，防止mChart未初始化
        renderChart();
    }
}, { deep: true });
</script>
