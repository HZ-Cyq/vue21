<template>

    <div class="chart-container">
        <div ref="chartItem" class="chart"></div>
    </div>
  </template>
  
  <script>
  import * as echarts from 'echarts'
  
  export default {
    name: 'TestItemTrigger',
    mounted() {
      const chart = echarts.init(this.$refs.chartItem)
  
      const option = {
        title: { text: 'Tooltip Trigger: item' },
        tooltip: { trigger: 'item' }, // <=== 按数据项触发
        legend: { data: ['温度', '湿度'] },
        xAxis: { type: 'category', data: ['10:00', '11:00', '12:00', '13:00', '14:00'] },
        yAxis: { type: 'value' },
        series: [
          { name: '温度', type: 'line', data: [20, 22, 24, 23, 25] },
          { name: '湿度', type: 'line', data: [50, 55, 53, 52, 58] }
        ]
      }
  
      chart.setOption(option)
    }
  }
  </script>
  
  <style scoped>
  .chart-container {
    width: 100%;
    height: 400px;
  }
  .chart {
    width: 100%;
    height: 100%;
  }
  </style>