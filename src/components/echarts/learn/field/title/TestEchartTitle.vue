<template>
    <div>
      <h2>ECharts Title 全面配置示例</h2>
      <div ref="chartRef" class="chart"></div>
    </div>
  </template>
  
  <script>
  import * as echarts from 'echarts'
  
  export default {
    name: 'TestEchartTitle',
    mounted() {
      const chart = echarts.init(this.$refs.chartRef)
  
      const option = {
        // Title 配置
        title: [
          {
            text: '主标题居中',          // 主标题文本
            subtext: '副标题示例\n多行副标题', // 副标题支持多行
            left: 'center',             // 水平居中
            top: 'top',
            textStyle: {
              color: '#5470C6',
              fontSize: 24,
              fontWeight: 'bold',
              textBorderColor: '#fff', // 文字描边颜色
              textBorderWidth: 2
            },
            subtextStyle: {
              color: '#999',
              fontSize: 14,
              fontStyle: 'italic'
            },
            backgroundColor: 'rgba(200,200,200,0.2)', // 背景色
            borderColor: '#5470C6',    // 边框颜色
            borderWidth: 1,
            padding: [10, 20, 10, 20], // 上右下左
            shadowColor: 'rgba(0,0,0,0.3)',
            shadowBlur: 10
          },
          {
            text: '主标题居左',           // 第二个标题
            subtext: '副标题在右边',
            left: 'left',
            top: '50px',
            textAlign: 'left',
            textStyle: { color: '#91CC75', fontSize: 20, fontWeight: 'bold' },
            subtextStyle: { color: '#666', fontSize: 12 },
          },
          {
            text: '主标题居右',
            subtext: '副标题示例',
            right: 20,
            top: 50,
            textAlign: 'right',
            textStyle: { color: '#EE6666', fontSize: 20, fontWeight: 'bold' },
            subtextStyle: { color: '#333', fontSize: 12 }
          },
          {
            text: "这里是默认标题，text字段",
            subtext: "这是默认的副标题，subText字段"
          }
        ],
  
        tooltip: { trigger: 'axis' },
  
        legend: {
          data: ['销量', '库存'],
          top: 120
        },
  
        xAxis: {
          type: 'category',
          data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
        },
  
        yAxis: {
          type: 'value'
        },
  
        series: [
          {
            name: '销量',
            type: 'bar',
            data: [120, 200, 150, 80, 70, 110, 130]
          },
          {
            name: '库存',
            type: 'line',
            data: [60, 80, 90, 70, 50, 60, 80],
            smooth: true
          }
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
    height: 450px;
  }
  </style>
  