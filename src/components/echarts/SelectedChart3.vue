<template>
    <div>
      <el-select v-model="selectedIds" multiple placeholder="选择TrajID" style="margin-bottom: 20px">
        <el-option v-for="id in availableIds" :key="id" :label="id" :value="id" />
      </el-select>
      <div ref="chart" style="height: 500px" />
    </div>
  </template>
  
  <script>
  import * as echarts from 'echarts'
  
  export default {
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
              axis_x: { fieldName: "time", units: "s", name: "时间" },
              axis_y_left: [
                { fieldName: "X", name: "朝向（度）", color: "#ffffff" },
                { fieldName: "Y", name: "俯仰(度)", color: "#ff00ff" }
              ],
              axis_y_right: [
                { fieldName: "Z", name: "滚转（度）", color: "#0000ff" }
              ]
            }
          ]
        },
        rawData: [
          ["time", "TrajID", "GroupID", "X", "Y", "Z"],
          [1, 1, 1, 22, 23, 31],
          [2, 1, 2, 32, 24, 32],
          [3, 1, 3, 42, 25, 33],
          [1, 2, 1, 12, 13, 32],
          [2, 2, 2, 13, 14, 33],
          [3, 2, 3, 14, 15, 34],
          [1, 3, 1, 2, 3, 3],
          [2, 3, 2, 3, 4, 4],
          [3, 3, 3, 4, 5, 5]
        ],
        selectedIds: [],
        availableIds: [],
        chart: null
      }
    },
    mounted() {
      this.chart = echarts.init(this.$refs.chart)
      this.extractAvailableIds()
      this.selectedIds = [...this.availableIds]
      this.renderChart()
    },
    watch: {
      selectedIds() {
        this.renderChart()
      }
    },
    methods: {
      extractAvailableIds() {
        const header = this.rawData[0]
        const rows = this.rawData.slice(1)
        const idIndex = header.indexOf(this.metric.data[0].filters)
        this.availableIds = Array.from(new Set(rows.map(row => row[idIndex])))
      },
      renderChart() {
        const header = this.rawData[0]
        const rows = this.rawData.slice(1)
  
        const config = this.metric.data[0]
        const xIndex = header.indexOf(config.axis_x.fieldName)
        const idIndex = header.indexOf(config.filters)
  
        const series = []
  
        const createSeries = (field, axisIndex = 0) => {
          const fieldIndex = header.indexOf(field.fieldName)
          this.selectedIds.forEach(id => {
            const data = rows
              .filter(row => row[idIndex] === id)
              .map(row => [row[xIndex], row[fieldIndex]])
            series.push({
              name: `${field.name} - ${id}`,
              type: 'line',
              yAxisIndex: axisIndex,
              data,
              lineStyle: { color: field.color },
              symbol: 'none'
            })
          })
        }
  
        config.axis_y_left.forEach(field => createSeries(field, 0))
        config.axis_y_right.forEach(field => createSeries(field, 1))
  
        const option = {
          title: { text: this.metric.title },
          tooltip: { trigger: 'axis' },
          legend: { type: 'scroll', top: 30 },
          xAxis: { name: config.axis_x.name, type: 'value' },
          yAxis: [
            { name: '左轴', type: 'value' },
            { name: '右轴', type: 'value' }
          ],
          series
        }
  
        this.chart.setOption(option)
      }
    }
  }
  </script>
  