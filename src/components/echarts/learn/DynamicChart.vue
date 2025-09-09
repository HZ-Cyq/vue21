<template>
    <div class="chart-container">
      <div ref="chartRef" class="chart"></div>
    </div>
  </template>
  
  <script>
  import * as echarts from 'echarts'
  
  export default {
    name: 'DynamicChart',
    data() {
      return {
        chart: null,
        timer: null,
        xData: [],  // X 轴数据
        yData: []   // Y 轴数据
      }
    },
    mounted() {
      this.initChart()
      this.startUpdate()
      window.addEventListener('resize', this.resizeChart)
    },
    beforeDestroy() {
      this.stopUpdate()
      window.removeEventListener('resize', this.resizeChart)
    },
    methods: {
      initChart() {
        this.chart = echarts.init(this.$refs.chartRef)
  
        // 初始化时先放一些默认数据
        for (let i = 0; i < 10; i++) {
          this.xData.push(new Date().toLocaleTimeString())
          this.yData.push(Math.round(Math.random() * 100))
        }
  
        const option = {
          title: { text: '动态折线图示例' },
          tooltip: { trigger: 'axis' },
          xAxis: {
            type: 'category',
            data: this.xData
          },
          yAxis: { type: 'value' },
          series: [
            {
              name: '随机值',
              type: 'line',
              smooth: true,
              data: this.yData
            }
          ]
        }
  
        this.chart.setOption(option)
      },
      startUpdate() {
        this.timer = setInterval(() => {
          // 模拟新数据
          const newTime = new Date().toLocaleTimeString()
          const newValue = Math.round(Math.random() * 100)
  
          // 保持最多 10 条
          if (this.xData.length >= 10) {
            this.xData.shift()
            this.yData.shift()
          }
  
          this.xData.push(newTime)
          this.yData.push(newValue)
  
          this.chart.setOption({
            xAxis: { data: this.xData },
            series: [{ data: this.yData }]
          })
        }, 2000)
      },
      stopUpdate() {
        if (this.timer) {
          clearInterval(this.timer)
          this.timer = null
        }
      },
      resizeChart() {
        if (this.chart) {
          this.chart.resize()
        }
      }
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
  