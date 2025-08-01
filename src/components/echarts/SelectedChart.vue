<template>
    <div style="padding: 20px">
      <!-- 多选框区域 -->
      <el-form label-position="top" label-width="100px">
        <el-form-item label="选择导弹编号">
          <el-checkbox-group v-model="selectedMissiles">
            <el-checkbox
              v-for="id in missileIds"
              :label="id"
              :key="id"
            />
          </el-checkbox-group>
        </el-form-item>
      </el-form>
  
      <!-- 折线图区域 -->
      <div ref="chart" style="width: 100%; height: 400px; margin-top: 20px" />
    </div>
  </template>
  <script>
  
  import * as echarts from 'echarts'
  export default {
    data() {
      return {
        rawData: `missile_id,time,altitude
  A,0,100
  A,1,150
  A,2,200
  B,0,120
  B,1,180
  B,2,240
  C,0,80
  C,1,130
  C,2,160`,
        parsedData: [],
        selectedMissiles: [],
        missileIds: [],
        chart: null,
      }
    },
    watch: {
      selectedMissiles() {
        this.renderChart()
      }
    },
    mounted() {
      this.parseData()
      this.chart = echarts.init(this.$refs.chart)
      this.renderChart()
    },
    methods: {
      parseData() {
        const lines = this.rawData.trim().split('\n')
        const [_, ...dataLines] = lines
        console.log(_);
        this.parsedData = dataLines.map(line => {
          const [missile_id, time, altitude] = line.split(',')
          return {
            missile_id,
            time: Number(time),
            altitude: Number(altitude)
          }
        })
        this.missileIds = Array.from(new Set(this.parsedData.map(d => d.missile_id)))
        this.selectedMissiles = [...this.missileIds]
      },
      renderChart() {
        let index = 0;
        const series = this.selectedMissiles.map(missile => {
          let color;
          if(index === 0) {
            color = "red"
          } else if(index === 1) {
            color = "blue"
          } else {
            color = 'yellow'
          }
          index ++;
          const missileData = this.parsedData.filter(d => d.missile_id === missile)
          return {
            name: missile,
            type: 'line',
            data: missileData.map(d => [d.time, d.altitude]),
            lineStyle: {color: color},
            symbol: 'none'
          }
        })
        
        this.chart.clear();

        const option = {
          tooltip: { trigger: 'axis' },
          legend: { 
            top: 'top',
            icon: 'rect'  // 去掉图例前面的圆圈 
          },
          animation: false,
          xAxis: {
            type: 'value',
            name: 'Time'
          },
          yAxis: {
            type: 'value',
            name: 'Altitude'
          },
          series
        }
        console.log(option);
        this.chart.setOption(option)
      }
    }
  }
  </script>
  