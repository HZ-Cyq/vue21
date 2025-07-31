<template>
    <div style="width: 600px; height: 400px">
      <v-chart :option="chartOptions" autoresize />
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        chartOptions: {
          title: { text: '柱状图示例' },
          tooltip: {},
          xAxis: {
            type: 'category',
            data: ['苹果', '香蕉', '橙子']
          },
          yAxis: {
            type: 'value'
          },
          series: [{
            name: '数量',
            type: 'bar',
            data: [5, 20, 36]
          }]
        }
      }
    }
  }
  </script>
  