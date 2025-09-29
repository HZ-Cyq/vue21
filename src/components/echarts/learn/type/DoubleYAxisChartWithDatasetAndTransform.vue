<template>
    <div>
      <h3>✅ 修复版：dataset.fromDatasetId 简单示例</h3>
      <div ref="chartRef" class="echart" style="width: 100%; height: 300px;"></div>
    </div>
  </template>
  
  <script>
  import * as echarts from 'echarts';
  
  export default {
    name: 'SimpleFromDatasetId',
    data() {
      return {
        chart: null
      };
    },
    mounted() {
      // 初始化图表
      this.chart = echarts.init(this.$refs.chartRef);
  
      // ✅ 确保是标准的对象数组，字段名清晰
      const sourceData = [
        { city: '北京', sales: 120 },
        { city: '上海', sales: 100 },
        { city: '广州', sales: 80 },
        { city: '深圳', sales: 90 }
      ];
  
      const option = {
        // 定义数据集
        dataset: [
          // 原始数据集
          {
            id: 'raw',
            source: sourceData  // ✅ 正确格式：对象数组
          },
          // 派生数据集，继承 raw
          {
            id: 'derived',
            fromDatasetId: 'raw'  // ✅ 正确引用
          }
        ],
        // 图表配置
        tooltip: { trigger: 'item' },
        xAxis: {
          type: 'category',
          name: '城市'
        },
        yAxis: {
          type: 'value',
          name: '销量'
        },
        series: [
          {
            type: 'bar',
            datasetId: 'derived',  // 使用 derived 数据集
            encode: {
              x: 'city',   // ✅ 字段名必须与 source 中的 key 完全一致
              y: 'sales'
            },
            name: '销量'
          }
        ]
      };
  
      // ✅ 确保 DOM 已就绪，再 setOption
      this.$nextTick(() => {
        this.chart.setOption(option);
      });
    },
    beforeDestroy() {
      if (this.chart) {
        this.chart.dispose();
        this.chart = null;
      }
    }
  };
  </script>