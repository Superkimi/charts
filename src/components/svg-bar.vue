<template>
  <button @click="changeData">change data</button>
  <svg :width="state.width" :height="state.height">
    <g stroke = '#5B8FF9' stroke-width = '2'>
      <!--x轴-->
      <!-- x代表横坐标从50 画到 750（x1 -> x2）y代表横坐标从550 画到 550（y1 -> y2）不变 -->
      <line x1 = '50' y1 = '550' x2 = '750' y2 = '550'></line>
      <!-- 同理：画出箭头 -->
      <line x1 = '740' y1 = '540' x2 = '750' y2 = '550'></line>
      <line x1 = '740' y1 = '560' x2 = '750' y2 = '550'></line>
      <!--Y轴-->
      <line x1 = '50' y1 = '50' x2 = '50' y2 = '550'></line>
      <line x1 = '50' y1 = '50' x2 = '40' y2 = '60'></line>
      <line x1 = '50' y1 = '50' x2 = '60' y2 = '60'></line>
    </g>
    <g v-for="(item, index) in state.data" :key="index">
      <!-- x是到viewbox左边的距离，y是到viewbox顶部的距离 -->
      <rect 
        :width="state.barWidth" 
        fill="#5B8FF9" 
        :height="item.value" 
        :x="state.barGap * (index + 1)" 
        :y="state.height - 50 - item.value"
      >
      </rect>
      <text 
        fill="#5B8FF9" 
        :x="state.barGap * (index + 1) + 10" 
        :y="state.height - 30"
      >
       {{ item.name }}
      </text>
      <text 
        fill="#5B8FF9" 
        :x="state.barGap * (index + 1) + 10" 
        :y="state.height - 55 - item.value"
      >
        {{ item.value }}
      </text>
    </g>
  </svg>
</template>

<script setup>
// SVG是一种用来描述二维矢量图形的XML标记语言，所以SVG标签不能使用document.createElement直接创建(浏览器无法识别)。
// 需要使用document.createElementNS创建一个具有指定的命名空间URI和限定名称的元素，SVG的命名空间是'http://www.w3.org/2000/svg'。
// 矢量图存储的是一幅图的结构数据，例如各个关键点的相对位置和比例等数据，当图形放大或缩小的时候，
// 矢量图图形是根据结构数据重新绘制的，因此几乎不失真。
import { reactive } from 'vue'

const state = reactive(
  { 
    data: [
      {"name": "到店", "value": 250},
      {"name": "快驴", "value": 470},
      {"name": "到家", "value": 200},
      {"name": "到综", "value": 180},
      {"name": "优选", "value": 100},
      {"name": "打车", "value": 340},
    ],
    width: 800,
    height: 600,
    barWidth: 50,
    barGap: 100,
  }
)

const changeData = function() {
  state.data = [
    {"name": "到店1", "value": 450},
    {"name": "快驴1", "value": 520},
    {"name": "到家1", "value": 100},
    {"name": "到综1", "value": 92},
    {"name": "优选1", "value": 14},
    {"name": "打车1", "value": 320},
  ]
}
</script>

<style scoped>
a {
  color: #42b983;
}
</style>
