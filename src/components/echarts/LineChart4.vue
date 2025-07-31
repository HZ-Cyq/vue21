<template>
    <div style="width: 100%; height: 600px" ref="chartContainer"></div>
  </template>
  
  <script>
  import * as echarts from "echarts";
  import "echarts-gl";
  
  export default {
    data() {
      return {
        points: []

      };
    },
    mounted() {
    this.points = Array.from({ length: 50 }, (_, i) => {
  return {
    time: i * 0.1,
    lon: 120 + 0.05 * i, // 经度从120开始，每个点+0.05
    lat: 30 + 0.02 * i,  // 纬度从30开始，每个点+0.02
    alt: 100 + 10 * Math.sin(i * 0.2) // 高度在100附近上下波动
  };
});

      const chart = echarts.init(this.$refs.chartContainer);
      const data = this.points.map(p => [p.lon, p.lat, p.alt]);
  
      chart.setOption({
        tooltip: {
          formatter: (params) => {
            const p = params.value;
            return `Lon: ${p[0]}<br/>Lat: ${p[1]}<br/>Alt: ${p[2]}`
          }
        },
        xAxis3D: { type: 'value', name: 'Longitude' },
        yAxis3D: { type: 'value', name: 'Latitude' },
        zAxis3D: { type: 'value', name: 'Altitude' },
        grid3D: {
          viewControl: {
            projection: 'perspective'
          }
        },
        series: [
          {
            type: 'line3D',
            data: data,
            lineStyle: {
              width: 4,
              color: '#3b7cff'
            }
          }
        ]
      });
    }
  };
  </script>
  