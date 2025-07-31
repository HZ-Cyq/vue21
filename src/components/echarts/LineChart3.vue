<template>
    <div id="app">
      <div ref="chart" style="width: 100%; height: 600px;"></div>
    </div>
  </template>
  
  <script>
  import * as echarts from 'echarts';
  
  export default {
    name: 'EchartsDualYAxis',
    data() {
      return {
        positionData: [],
        speedData: [],
      };
    },
    mounted() {
      this.generateData();
      this.initChart();
    },
    methods: {
      generateData() {
        const points = [];
        const speeds = [];
        let x = 100, y = 100;
  
        for (let i = 0; i < 50; i++) {
          x += Math.random() * 20 - 10;
          y += Math.random() * 20 - 10;
          points.push([x.toFixed(2), y.toFixed(2)]);
          const speed = (Math.random() * 20 + 5).toFixed(2);
          speeds.push(speed);
        }
  
        this.positionData = points;
        this.speedData = speeds;
      },
      initChart() {
        const chart = echarts.init(this.$refs.chart);
  
        const option = {
          tooltip: {
            trigger: 'axis',
          },
          xAxis: {
            type: 'category',
            data: this.positionData.map((_, i) => i + 1),
            name: '序号',
          },
          yAxis: [
            {
              type: 'value',
              name: '位置',
              position: 'left',
            },
            {
              type: 'value',
              name: '速度',
              position: 'right',
            }
          ],
          series: [
            {
              name: '位置轨迹',
              type: 'line',
              data: this.positionData.map((p) => Number(p[1])),
              yAxisIndex: 0,
              smooth: true,
              lineStyle: {
                color: '#5470C6'
              },
            },
            {
              name: '速度',
              type: 'line',
              data: this.speedData.map(Number),
              yAxisIndex: 1,
              smooth: true,
              lineStyle: {
                color: '#91cc75'
              },
            }
          ]
        };
  
        chart.setOption(option);
      },
    },
  };
  </script>
  
  <style scoped>
  #app {
    width: 100%;
  }
  </style>
  