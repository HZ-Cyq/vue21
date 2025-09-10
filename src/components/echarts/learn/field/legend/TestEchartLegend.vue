<template>
    <div>
      <h2>ECharts Legend 使用示例</h2>
      <div ref="chartRef" class="chart"></div>
    </div>
  </template>
  
  <script>
  import * as echarts from 'echarts'
  
  export default {
    name: 'TestEchartLegend',
    mounted() {
      const chart = echarts.init(this.$refs.chartRef)
  
      const option = {
        title: {
          text: 'Legend 示例',
        },
  
        // 🔹 tooltip 提示
        tooltip: { trigger: 'axis' },
  
        // 🔹 legend 配置
        legend: {
          data: ['温度', '湿度', '风速'], // 图例名称，对应 series.name
        //   top: 60,                        // 距离顶部 60px
          // left: 'right',                 // 水平居中
        //   orient: 'horizontal',           // 水平显示，'vertical' 竖直显示
        //   textStyle: { fontSize: 14, color: '#333' }, // 文字样式
        //   backgroundColor: 'rgba(200,200,200,0.1)',   // 背景色
          borderColor: '#333',            // 边框颜色
        //   borderWidth: 1,
        //   padding: [5, 10, 5, 10],        // 上右下左
        //   itemWidth: 100,                  // 图例小方块宽度
          itemHeight: 10,                 // 图例小方块高度
        //   selected: {                      // 初始选中状态，可控制默认隐藏/显示
        //     '风速': false
        //   }
        },
  
        xAxis: {
          type: 'category',
          data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
        },
  
        yAxis: { type: 'value' },
  
        series: [
          { name: '温度', type: 'line', smooth: true, data: [20, 22, 24, 23, 25, 26, 24] },
          { name: '湿度', type: 'line', smooth: true, data: [50, 55, 53, 52, 58, 57, 56] },
          { name: '风速', type: 'line', smooth: true, data: [5, 6, 5, 7, 6, 5, 4] }
        ]
      }
  
      chart.setOption(option)
  
      // 窗口自适应
      window.addEventListener('resize', () => chart.resize())
    }
  }
  </script>
  
  <style scoped>
  .chart {
    width: 100%;
    height: 400px;
  }
  </style>
  