<template>
    <div>
      <div ref="chart" style="width: 100%; height: 400px"></div>
    </div>
  </template>
  
  <script>
  import * as echarts from "echarts";
  
  export default {
    name: "ScatterChart",
    data() {
      return {
        chart: null,
        rawData: [
          {
            index: 3,
            info: [
              { name: "PAC3_1", value: 10.787 },
            //   { name: "PAC3_2", value: 100 }
            ]
          },
          {
            index: 2,
            info: [
              { name: "PAC3_1", value: 10 },
              { name: "PAC3_2", value: 102 }
            ]
          },
          {
            index: 1,
            info: [
              { name: "PAC3_1", value: 10 },
              { name: "PAC3_2", value: 103 }
            ]
          }
        ]
      };
    },
    mounted() {
      this.chart = echarts.init(this.$refs.chart);
      this.renderChart();
    },
    methods: {
      renderChart() {
        // 获取所有 name
        const names = [...new Set(this.rawData.flatMap(d => d.info.map(i => i.name)))];
  
        // 每个 name 构建一个 series
        const series = names.map(name => ({
          name,
          type: "scatter",
          symbolSize: 12,
          data: this.rawData.map(d => {
            const item = d.info.find(i => i.name === name);
            return [d.index, item ? item.value : null];
          })
        }));
  
        const option = {
          title: {
            text: "脱离靶量散点图"
          },
          tooltip: {
            trigger: "item",
            formatter: params => {
              return `${params.seriesName}<br/>仿真次数: ${params.value[0]}<br/>脱离靶量: ${params.value[1]}`;
            }
          },
          legend: {
            data: names
          },
          xAxis: {
            type: "value",
            name: "仿真次数",
            // min: 0,
            // max: 4,
            interval: 1
          },
          yAxis: {
            type: "value",
            name: "脱离靶量(米)"
          },
          series
        };
        console.log(JSON.stringify(option));
        this.chart.setOption(option);
      }
    }
  };
  </script>
  