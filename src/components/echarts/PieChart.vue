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
          title: { text: '饼图示例', left: 'center' },
          tooltip: { trigger: 'item' },
          legend: { orient: 'vertical', left: 'left' },
          series: [{
            name: '访问来源',
            type: 'pie',
            radius: '50%',
            data: [
              { value: 1048, name: '搜索引擎' },
              { value: 735, name: '直接访问' },
              { value: 580, name: '邮件营销' }
            ]
          }]
        }
      }
    }
  }
  </script>
  