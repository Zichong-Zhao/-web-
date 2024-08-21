<template>
	<div>
		<div>【云端报警风险】</div>
		<div ref="target" class="w-full h-full"></div>
	</div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref, watch } from 'vue'
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

onBeforeUnmount(() => {
	if (mChart) {
		mChart.dispose()
	}
})

const renderChart = () => {
    if (!mChart) return; // 增加错误处理，防止mChart未初始化

    const path = "image:////img.isqqw.com/profile/upload/2023/11/15/fabee49f-3e3b-45a6-a5d5-2780f5684bde.png";

    const option = {
        backgroundColor:'transparent',
        tooltip: {
            show: false,
            trigger: "item",
        },
        radar: {
            center: ["50%", "50%"],
            radius: "80%",
            startAngle: 240,
            splitNumber: 5,
            splitArea: {
                areaStyle: {
                    color: [
                        'rgba(0,96,208, 0.1)', 'rgba(0,96,208, 0.2)',
                        'rgba(0,96,208, 0.4)', 'rgba(0,96,208, 0.6)',
                        'rgba(0,96,208, 0.8)', 'rgba(0,96,208, 1)'
                    ].reverse()
                }
            },
            axisLabel: {
                show: false,
            },
            axisLine: {
                show: true,
                lineStyle: {
                    color: "transparent"
                }
            },
            splitLine: {
                show: true,
                lineStyle: {
                    color: "transparent"
                }
            },
            name: {
                formatter:function(value){
                    const item = props.data.risks.find(item => item.name === value);
                    if (!item) return value; // 处理找不到的情况

                    const percent = item.value;
                    return '{b|' + value + '} {a|' + percent + '}'; // 调整顺序，使value在name后面，并添加冒号
                },
                textStyle: {
                    rich: {
                        a: {
                            color: '#FFFFFF',
                            fontSize: 13,
                            padding: [0, 0],
                            lineHeight:20,
                        },
                        b: {
                            color: '#87CEEB',
                            fontSize: 14,
                            padding: [0, 0],
                            lineHeight:20,
                        }
                    },
                },
            },
            indicator: props.data.risks.map((item) => ({
                name: item.name,
                max:100
            })),
        },

        series: [{
            type: "radar",
            symbol: "circle",
            symbolSize: 7,
            areaStyle: {
                normal: {
                    color: 'rgba(170, 216, 255, 0)',
                }
            },
            itemStyle: {
                color: '#84E1FF',
                borderColor: '#00A7FE',
                borderWidth: 1,
            },
            lineStyle: {
                normal: {
                    color: "#00A7FE",
                    width: 2
                }
            },
            data: [{
              value:props.data.risks.map(item => item.value)
            }]
        }]
    };

    mChart.setOption(option);
}

watch(() => props.data.risks, () => {
    if (mChart) { // 增加错误处理，防止mChart未初始化
        renderChart();
    }
}, { deep: true });
</script>

<style scoped>
.w-full {
	width: 100%;
}
.h-full {
	height: 100%;
}
</style>
