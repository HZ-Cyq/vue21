<template>
    <div>
      <multi-axis-line-chart :metric="metric" :data="data" />
    </div>
  </template>
  
  <script>
  import MultiAxisLineChart from "@/components/echarts/learn/MultiAxisLineChart.vue";
  
  export default {
    components: { MultiAxisLineChart },
    data() {
      return {
        metric: {
          title: "进攻弹姿态分析图",
          display: "column",
          data: [
            {
              type: "LINE",
              tableName: "offensive_Missile_Traj",
              filters: "TrajID",
              axis_x: {
                fieldName: "time",
                units: "s",
                name: "时间",
              },
              axis_y_left: [
                {
                  fieldName: "roll",
                  units: "°",
                  name: "滚转角",
                  color: "#ff0000",
                },
                {
                  fieldName: "pitch",
                  units: "°",
                  name: "俯仰角",
                  color: "#00ff00",
                },
              ],
              axis_y_right: [
                {
                  fieldName: "speed",
                  units: "m/s",
                  name: "速度",
                  color: "#0000ff",
                },
              ],
            },
          ][0], // 示例只用第一个
        },
        data: [
          { time: 0, roll: 10, pitch: 20, speed: 200 },
          { time: 1, roll: 15, pitch: 25, speed: 210 },
          { time: 2, roll: 20, pitch: 30, speed: 220 },
          // ...
        ],
      };
    },
  };
  </script>
  