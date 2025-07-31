<template>
    <div>
      <div ref="chart" style="width: 100%; height: 400px;"></div>
    </div>
  </template>
  
  <script>
  // 引入 ECharts
  import * as echarts from 'echarts';
  
  export default {
    name: 'HeightOverTimeChart',
    data() {
      return {
        rawData: [
        //   { time: 0.1, alt: 0 },
        //   { time: 0.2, alt: 1 },
        //   { time: 0.3, alt: 2 },
        //   { time: 0.4, alt: 3 },
        //   { time: 0.5, alt: 4 },
        //   { time: 0.6, alt: 5 },
        //   { time: 0.7, alt: 6 },
        //   { time: 0.8, alt: 7 },
        //   { time: 0.9, alt: 6 },
        //   { time: 1.0, alt: 5 },
        //   { time: 1.1, alt: 4 },
        //   { time: 1.2, alt: 3 },
        //   { time: 1.3, alt: 2 },
        //   { time: 1.4, alt: 1 }
        ]
      };
    },
    mounted() {
      for(let i = 0; i < 1000; i++) {
        let obj = {
            time: i,
            alt: i
        }
        this.rawData.push(obj);   
      }
      this.initChart();
    },
    methods: {
      initChart() {
        const chart = echarts.init(this.$refs.chart);
        const times = this.rawData.map(item => item.time);
        const alts = this.rawData.map(item => item.alt);
  
        const option = {
          title: {
            text: '时间-高度变化图',
            left: 'center'
          },
          tooltip: {
            trigger: 'axis',
            formatter: params => {
              const { data, axisValue } = params[0];
              return `时间: ${axisValue}<br/>高度: ${data}`;
            }
          },
          xAxis: {
            type: 'category',
            name: '时间（s）',
            data: times
          },
          yAxis: {
            type: 'value',
            name: '高度（m）'
          },
          series: [
            {
              name: '高度',
              type: 'line',
              data: alts,
              smooth: true,
              symbol: 'circle',
              symbolSize: 6,
              lineStyle: {
                width: 2
              },
              areaStyle: {
                color: 'rgba(0, 128, 255, 0.1)'
              }
            }
          ]
        };
  
        chart.setOption(option);
        window.addEventListener('resize', () => chart.resize());
      }
    }
  };
  </script>
  
  <style scoped>
  </style>
  