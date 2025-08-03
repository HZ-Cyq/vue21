<template>
    <div>
      <div style="margin-bottom: 8px">
        <strong>字段选择：</strong>
        <label v-for="f in yFields" :key="f.field" style="margin-right: 12px">
          <input type="checkbox" v-model="visibleFields" :value="f.field" />
          {{ f.label }}
        </label>
      </div>
  
      <div style="margin-bottom: 12px">
        <strong>分组选择（{{ groupField }}）：</strong>
        <!-- 新增全选框 -->
        <label style="margin-right: 12px; font-weight: bold;">
          <input type="checkbox" v-model="allGroupsSelected" />
          全选
        </label>
  
        <label v-for="id in allGroups" :key="id" style="margin-right: 12px">
          <input type="checkbox" v-model="visibleGroups" :value="id" />
          {{ id }}
        </label>
      </div>
  
      <div style="width: 800px; height: 500px">
        <v-chart :option="chartOptions" autoresize />
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      const rawSource = [
        ["time", "TrajID", "Latitude", "Longitude", "Altitude"],
        [1, 1, 10, 2, 0],
        [12, 1, 2, 3, 1],
        [31, 1, 3, 4, 2],
        [41, 1, 4, 5, 3],
        [51, 1, 5, 6, 4],
        [1, 2, 1, 1, 0],
        [12, 2, 4, 1, 1],
        [31, 2, 2, 1, 2],
        [41, 2, 5, 1, 3],
        [51, 2, 6, 1, 4],
        [1, 3, 7, 10, 0],
        [12, 3, 4, 11, 1],
        [31, 3, 9, 11, 2],
        [41, 3, 10, 11, 3],
        [51, 3, 22, 11, 4],
      ];
  
      const groupField = "TrajID";
  
      const yFields = [
        { field: "Latitude", label: "纬度", color: "#5470C6" },
        { field: "Longitude", label: "经度", color: "#91CC75" }
      ];
  
      const xAxis = { type: "value", name: "时间(s)" };
      const yAxis = { type: "value", name: "数值(度)" };
  
      const header = rawSource[0];
      const rows = rawSource.slice(1);
  
      const groupIndex = header.indexOf(groupField);
      const allGroups = Array.from(new Set(rows.map(r => r[groupIndex])));
  
      const dataset = [
        { id: "raw", source: rawSource },
        ...allGroups.map(id => ({
          id: `group${id}`,
          fromDatasetId: "raw",
          transform: {
            type: "filter",
            config: { dimension: groupField, eq: id }
          }
        }))
      ];
  
      return {
        rawSource,
        groupField,
        yFields,
        xAxis,
        yAxis,
        dataset,
        allGroups,
        visibleGroups: [...allGroups],       // 默认全部显示
        visibleFields: yFields.map(f => f.field) // 默认所有字段
      };
    },
  
    computed: {
      // 新增计算属性，绑定全选checkbox
      allGroupsSelected: {
        get() {
          return this.visibleGroups.length === this.allGroups.length;
        },
        set(value) {
          this.visibleGroups = value ? [...this.allGroups] : [];
        }
      },
  
      chartOptions() {
        const series = [];
        this.visibleGroups.forEach(groupId => {
          this.yFields.forEach(({ field, label, color }) => {
            if (this.visibleFields.includes(field)) {
              series.push({
                type: "line",
                name: `${this.groupField} ${groupId} - ${label}`,
                datasetId: `group${groupId}`,
                encode: { x: "time", y: field },
                lineStyle: { color },
                itemStyle: { color }
              });
            }
          });
        });
  
        return {
          tooltip: {},
          legend: { top: "top" },
          xAxis: this.xAxis,
          yAxis: this.yAxis,
          dataset: this.dataset,
          series
        };
      }
    }
  };
  </script>
  