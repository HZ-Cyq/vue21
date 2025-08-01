<template>
  <div>
    <div ref="chart" style="width: 100%; height: 500px;"></div>
  </div>
</template>

<script>
import * as echarts from "echarts";

export default {
  name: "OffensiveAttitudeChart",
  props: {
    metric: {
      type: Object,
      required: true,
    },
    data: {
      type: Array,
      required: true,
    },
  },
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chartDom = this.$refs.chart;
      const myChart = echarts.init(chartDom);

      const { axis_x, axis_y_left, axis_y_right, data: seriesDefs } = this.metric;
      console.log(seriesDefs);
      // x轴字段名
      const xField = axis_x.fieldName;
      const xUnit = axis_x.units || "";
      const xName = axis_x.name || "";

      // y轴字段名
      const yLeftFields = (axis_y_left || []).map((y) => ({
        field: y.fieldName,
        name: y.name,
        unit: y.units || "",
        color: y.color || undefined,
      }));

      const yRightFields = (axis_y_right || []).map((y) => ({
        field: y.fieldName,
        name: y.name,
        unit: y.units || "",
        color: y.color || undefined,
      }));

      const series = [];
      const colorList = [];

      // 所有字段统一处理
      const allYFields = [
        ...yLeftFields.map((f) => ({ ...f, yAxisIndex: 0 })),
        ...yRightFields.map((f) => ({ ...f, yAxisIndex: 1 })),
      ];

      allYFields.forEach((yField, idx) => {
        console.log(idx);
        const { field, name, unit, yAxisIndex, color } = yField;
        const seriesData = this.data.map((item) => [item[xField], item[field]]);
        series.push({
          name: name + (unit ? ` (${unit})` : ""),
          type: "line",
          yAxisIndex,
          data: seriesData,
          showSymbol: false,
          smooth: true,
          lineStyle: {
            width: 2,
            color: color || undefined,
          },
        });

        if (color) colorList.push(color);
      });

      const option = {
        title: {
          text: this.metric.title || "",
        },
        tooltip: {
          trigger: "axis",
        },
        legend: {
          top: "10%",
        },
        grid: {
          top: "20%",
          left: "8%",
          right: "8%",
          bottom: "10%",
        },
        xAxis: {
          type: "category",
          name: xName + (xUnit ? ` (${xUnit})` : ""),
          data: this.data.map((item) => item[xField]),
        },
        yAxis: [
          {
            type: "value",
            name: axis_y_left.map((y) => y.name).join(", "),
          },
          {
            type: "value",
            name: axis_y_right.map((y) => y.name).join(", "),
          },
        ],
        color: colorList.length > 0 ? colorList : undefined,
        series,
      };

      myChart.setOption(option);
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
  },
};
</script>
