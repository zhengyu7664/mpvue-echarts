<template>
  <div class="container">
    <div class="wrap">
      <mpvue-echarts :echarts="echarts" :onInit="onInit" />
    </div>
  </div>
</template>

<script>
import * as echarts from 'echarts/dist/echarts.simple.min'
import mpvueEcharts from 'mpvue-echarts'

function initChart (canvas, width, height) {
  const chart = echarts.init(canvas, null, {
    width: width,
    height: height
  })
  canvas.setChart(chart)

  var option = {
    backgroundColor: '#fff',
    color: ['#37A2DA', '#67E0E3', '#9FE6B8'],
    tooltip: {
      show: true,
      trigger: 'axis'
    },
    title: {
      text: '测试下面legend的红色区域不应被裁剪',
      left: 'center'
    },
    legend: {
      data: ['A', 'B', 'C'],
      top: 50,
      left: 'center',
      backgroundColor: 'red',
      z: 100
    },
    grid: {
      containLabel: true
    },
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
      // show: false
    },
    yAxis: {
      x: 'center',
      type: 'value',
      splitLine: {
        lineStyle: {
          type: 'dashed'
        }
      }
      // show: false
    },
    series: [{
      name: 'A',
      type: 'line',
      smooth: true,
      data: [18, 36, 65, 30, 78, 40, 33]
    }, {
      name: 'B',
      type: 'line',
      smooth: true,
      data: [12, 50, 51, 35, 70, 30, 20]
    }, {
      name: 'C',
      type: 'line',
      smooth: true,
      data: [10, 30, 31, 50, 40, 20, 10]
    }]
  }

  chart.setOption(option)
  return chart
}

export default {
  data () {
    return {
      echarts,
      onInit: initChart
    }
  },

  components: {
    mpvueEcharts
  },

  onShareAppMessage () {}
}
</script>

<style scoped>
.wrap {
  width: 100%;
  height: 300px;
}
</style>
