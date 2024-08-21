<template>
	<div>
		<div>【数据传递信息】</div>
		<div ref="target" class="w-full h-full"></div>
	</div>
</template>

<script setup>
import { onMounted, ref, watch } from 'vue'
import * as echarts from 'echarts'

const props = defineProps({
	data: {
		type: Object,
		required: true
	}
})

// 获取 dom 实例
const target = ref(null)

// echarts 实例变量
let mChart = null
// 在 mounted 生命周期之后，实例化 echarts
onMounted(() => {
	mChart = echarts.init(target.value)
	// 渲染 echarts
	renderChart()
})

// 渲染图表
const renderChart = () => {
	const options = {
		// X 轴不需要展示
		xAxis: {
			show: false,
			type: 'value'
		},
		// X 轴不需要展示
		yAxis: {
			show: false,
			type: 'value'
		},
		// 核心数据配置
		series: [
			{
				// 用于展现节点以及节点之间的关系数据
				type: 'graph',
				// 不采用任何布局
				layout: 'none',
				// 使用二维的直角坐标系
				coordinateSystem: 'cartesian2d',
				// 节点标记的大小
				symbolSize: 26,
				// z-index
				z: 1,
                // 边界标签（线条文字）
                edgeLabel: {
					normal: {
						show: true,
						color: '#fff',
						textStyle: {
							fontSize: 12,
						},
						formatter: function (params) {
							let txt = ''
							if (params.data.speed !== undefined) {
								txt = params.data.speed
							}
							return txt
						}
					}
				},
                // z-index
				z: 3,
				// 圆饼下文字
				label: {
					normal: {
						show: true,
						position: 'inside', // 将位置设置为 inside，使其显示在节点的中心
						color: '#fff'
					},

				},
				// 边两端的标记类型
				edgeSymbol: ['none', 'arrow'],
				// 边两端的标记大小
				edgeSymbolSize: 8,
// 圆数据
data: props.data.relations.map((item) => {
	// id 为 0 ，表示数据中心，数据中心单独设置
	if (item.id !== 0) {
		return {
			name: item.name,
			category: 0,
			active: true,
			speed: `${item.speed}kb/s`,
			// 位置
            value: item.value,
            // 节点的形状
			symbol: 'circle',
            // 节点的大小
			symbolSize: 42,
            // 小圆的样式
			itemStyle: {
				normal: {
					// 渐变色
					color: {
						colorStops: [
							{ offset: 0, color: '#87CEEB' },
							{ offset: 1, color: '#84E1FF' }
						]
					}
				}
			},
			// 字体
			label: {
				normal: {
					show: true,
					position: 'inside', // 将位置设置为 inside，使其显示在节点的中心
					color: 'BLACK',
					fontSize: 9 // 调整字体大小到 8
				}
			}
		}
	} else {
		return {
			name: item.name,
			// 位置
			value: item.value,
			// 数据中心圆的大小
			symbolSize: 100,
			// 圆的样式
			itemStyle: {
				normal: {
					// 渐变色
					color: {
						colorStops: [
							{ offset: 0, color: '#157eff' },
							{ offset: 1, color: '#35c2ff' }
						]
					}
				}
			},
			// 字体
			label: { 
                normal: { 
                    fontSize: '18',
                    color: 'BLACK',
                } 
            }
		}
	}
}),

				// 线
				links: props.data.relations.map((item, index) => ({
					// 方向
					source: item.source,
					target: item.target,
					// 线上的文字
					speed: `${item.speed}kb/s`,
					// 线的样式
					lineStyle: { normal: { color: '#12b5d0', curveness: 0.2 } },
					// 文字位置
					label: {
						show: true,
						position: 'middle',
						offset: [15, 0]
					}
				}))
			},
			{
				// 用于带有起点和终点信息的线数据的绘制
				type: 'lines',
				// 使用二维的直角坐标系
				coordinateSystem: 'cartesian2d',
				// z-index
				z: 2, // 调整 z 值以确保线特效不被挡住
				// 线特效的配置
				effect: {
					show: true,
					smooth: false,
					trailLength: 0,
					symbol: 'arrow',
					color: 'rgba(55,155,255,0.5)',
					symbolSize: 8
				},
				// 线的样式
				lineStyle: {
					normal: {
						curveness: 0.2
					}
				},
				// 线的数据级，前后线需要重合。数据固定
				data: [
					[{ coord: [0, 300] }, { coord: [50, 200] }],
					[{ coord: [0, 100] }, { coord: [50, 200] }],
					[{ coord: [50, 200] }, { coord: [100, 100] }],
					[{ coord: [50, 200] }, { coord: [100, 300] }]
				],
			}
		]
	}

	mChart.setOption(options)
}

// 监听数据的变化，重新渲染图表
watch(
	() => props.data,
	() => {
		renderChart()
	}
)
</script>

<style lang="scss" scoped></style>

