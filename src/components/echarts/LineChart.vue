<template>
    <div style="width: 600px; height: 400px">
      <v-chart :option="chartOptions" autoresize />
    </div>
  </template>
  
  <script>
  export default {
    
    mounted() {
      console.log("LineChart mounted");
    },

    data() {
      return {
        chartOptions: {
          title: { text: '折线图示例' },
          tooltip: {},
          xAxis: {
            type: 'category',
            data: ['一月', '二月', '三月', '四月']
          },
          yAxis: {
            type: 'value'
          },
          series: [{
            name: '销量',
            type: 'line',
            data: [10, 20, 15, 30]
          }]
        }
      }
    }

  }
  </script>
  