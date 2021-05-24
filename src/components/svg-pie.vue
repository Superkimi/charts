<template>
	<button @click="changeData">change data</button>
	<svg :width="state.width" :height="state.height">
		<g v-for="(item, index) in setting" :key="index">
			<path :fill="item.color" :d="getPath(item)"></path>
			<polyline
				:stroke="item.color"
				:stroke-width="0.5"
				:points="getLabelLine(item)"
				fill="none"
			></polyline>
			<text
				:x="item.lineEnd[0]"
				:y="item.lineEnd[1]"
				:dy="-2"
				:fill="item.color"
				font-size="12px"
				:text-anchor="item.isLeft ? 'start' : 'end'"
			>
				{{ item.value }}%
			</text>
			<text
				:x="item.lineEnd[0]"
				:y="item.lineEnd[1]"
				:dy="14"
				:fill="item.color"
				font-size="12px"
				:text-anchor="item.isLeft ? 'start' : 'end'"
			>
				{{ item.name }}
			</text>
		</g>
		<text
			:x="center[0] - state.r + 20"
			:y="center[1] + state.r + 15"
			fill="#000"
			font-size="30px"
		>
			总数量:400
		</text>
	</svg>
	<!-- <svg width="800" height="600">
		<path d = "M40,20 A30,30 0 0,0 70,70" style="stroke: #cccc00;stroke-width:2; fill:none;" /> 
    <path d = "M40,20 A30,30 0 1,0 70,70" style="stroke: #ff0000; stroke-width:2; fill:none;"/> 
    <path d = "M40,20 A30,30 0 1,1 70,70" style = "stroke: #00ff00; stroke-width:2; fill:none;"/> 
    <path d = "M40,20 A30,30 0 0,1 70,70" style = "stroke: #0000ff; stroke-width:2; fill:none;"/>
	</svg> -->
</template>

<script setup>
import { reactive, computed } from 'vue';

const state = reactive({
	data: [
		{
			value: 4.94,
			name: '快驴',
		},
		{
			value: 4.78,
			name: '到家',
		},
		{
			value: 4.0,
			name: '到店',
		},
		{
			value: 3.0,
			name: '优选',
		},
		{
			value: 49.4,
			name: '买菜',
		},
		{
			value: 28.77,
			name: '打车',
		},
	],
	colors: ['#5B8FF9', '#5AD8A6', '#5D7092', '#F6BD16', '#E8684A', '#6DC8EC'],
	width: 800,
	height: 600,
	R: 140,
	r: 100,
});
const center = [state.width / 2, state.height / 2];
const setting = computed(() => {
	const twoPi = Math.PI * 2;
	const total = state.data.reduce((memo, item) => memo + item.value, 0);
	let setting = state.data.slice();
	let startAngel = 0;
	let endAngel = 0;
	function getPoint(r, angel, center) {
		return [center[0] + Math.sin(angel) * r, center[0] - Math.cos(angel) * r];
	}

	setting.forEach((item, index) => {
		item.isLeft = false;
		endAngel = startAngel + (item.value / total) * twoPi; // 计算结束弧度
		item.pointArr = [
			getPoint(state.r, startAngel, center), // 计算P0坐标
			getPoint(state.R, startAngel, center), // 计算P1坐标
			getPoint(state.R, endAngel, center), // 计算P2坐标
			getPoint(state.r, endAngel, center), // 计算P3坐标
		];
		item.color = state.colors[index];
		// 大于Math.PI需要画大弧，否则画小弧
		item.LargeArcFlag = endAngel - startAngel > Math.PI ? '1' : '0';
		// 计算label折线起始点的位置
		item.lineStart = getPoint(
			state.R,
			startAngel + (endAngel - startAngel) / 2,
			center
		);
		// 线条label折线起点x值小于原点x值，在左侧，否则在右侧
		item.isLeft = item.lineStart[0] < center[0];
		// 根据线条起点左右，设置结束点, (0: svg最左边， width: 最右边)
		item.lineEnd = [
			item.isLeft ? center[0] - state.R - 100 : center[0] + state.R + 100,
			item.lineStart[1] - 60 / (index + 1),
		];
		startAngel = endAngel; // 将下一项数据的起始弧度设置为当前项的结束弧度
	});
	return setting;
});

function getPath(v) {
	let P = v.pointArr;
	return `M ${P[0][0]} ${P[0][1]} L ${P[1][0]} ${P[1][1]} A ${state.R} ${state.R} 0 ${v.LargeArcFlag} 1 ${P[2][0]} ${P[2][1]} L ${P[3][0]} ${P[3][1]} A ${state.r} ${state.r}  0 ${v.LargeArcFlag} 0 ${P[0][0]} ${P[0][1]} z`;
}

function getLabelLine(v) {
	return `${v.lineStart.join(' ')}, ${v.lineEnd[0] + (v.isLeft ? 50 : -50)}  ${
		v.lineEnd[1]
	}, ${v.lineEnd.join(' ')}`;
}

function changeData() {
	state.data = [
		{
			value: 27.67,
			name: '快驴1',
		},
		{
			value: 43.12,
			name: '到家1',
		},
		{
			value: 22.68,
			name: '到店1',
		},
		{
			value: 30.0,
			name: '优选1',
		},
		{
			value: 67.4,
			name: '买菜1',
		},
		{
			value: 87.66,
			name: '打车1',
		},
	];
}
</script>

<style scoped></style>
