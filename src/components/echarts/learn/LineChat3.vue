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
      <strong>分组选择（{{ groupField.join(' > ') }}）：</strong>
      <el-tree
        ref="groupTree"
        :data="treeData"
        show-checkbox
        node-key="id"
        default-expand-all
        :default-checked-keys="visibleGroups"
        @check-change="handleCheckChange"
        :props="defaultProps"
        style="max-height: 300px; overflow-y: auto; border: 1px solid #ebeef5; padding: 8px;"
      >
        <span class="custom-tree-node" slot-scope="{ data }">
          {{ data.label }}
        </span>
      </el-tree>
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
      ["time", "TrajID", "Gid", "Latitude", "Longitude", "Altitude"],
      [1, 1, 1, 10, 2, 0],
      [12, 1, 1, 2, 3, 1],
      [31, 1, 1, 3, 4, 2],
      [41, 1, 1, 4, 5, 3],
      [51, 1, 1, 5, 6, 4],
      [1, 2, 1, 1, 1, 1],
      [12, 2, 1, 4, 1, 1],
      [31, 2, 1, 2, 1, 2],
      [41, 2, 1, 5, 1, 3],
      [51, 2, 1, 6, 1, 4],
      [1, 2, 2, 11, 11, 0],
      [12, 2, 2, 14, 13, 1],
      [31, 2, 2, 12, 14, 2],
      [41, 2, 2, 15, 11, 3],
      [51, 2, 2, 16, 1, 4],
      [1, 3, 2, 7, 10, 0],
      [12, 3, 2, 4, 11, 1],
      [31, 3, 2, 9, 11, 2],
      [41, 3, 2, 10, 11, 3],
      [51, 3, 2, 22, 11, 4],
    ];

    const groupField = ["TrajID"];

    const yFields = [
      { field: "Latitude", label: "纬度", color: "#5470C6", yAxisIndex: 0 },
      { field: "Longitude", label: "经度", color: "#91CC75", yAxisIndex: 0 },
      { field: "Altitude", label: "高度", color: "#EE6666", yAxisIndex: 1 } // ← 配置在右轴
    ];

    const xAxis = { type: "value", name: "时间(s)" };
    const yAxis = { type: "value", name: "数值(度)" };
    const y2Axis = { type: "value", name: "高度(米)", position: "right" };
    // const y2Axis = {};

    const header = rawSource[0];
    const rows = rawSource.slice(1);

    const buildTree = (data, level = 0, parentPath = []) => {
      const field = groupField[level];
      if (!field) return [];
      const index = header.indexOf(field);
      const grouped = {};
      data.forEach(row => {
        const key = row[index];
        if (!grouped[key]) grouped[key] = [];
        grouped[key].push(row);
      });
      return Object.entries(grouped).map(([key, subRows]) => {
        const newPath = [...parentPath, key];
        const id = newPath.join("_");
        const node = { id, label: `${field}: ${key}` };
        const children = buildTree(subRows, level + 1, newPath);
        if (children.length) node.children = children;
        return node;
      });
    };

    const treeData = [
      {
        id: "all",
        label: "全部",
        children: buildTree(rows, 0, [])
      }
    ];

    const visibleGroups = [];
    const collectLeafIds = nodes => {
      nodes.forEach(node => {
        if (node.children) {
          collectLeafIds(node.children);
        } else {
          visibleGroups.push(node.id);
        }
      });
    };
    collectLeafIds(treeData);

    const dataset = [
      { id: "raw", source: rawSource },
      ...visibleGroups.map(groupKey => {
        const parts = groupKey.split("_");
        const filterConds = parts.map((val, i) => ({
          dimension: groupField[i],
          eq: isNaN(val) ? val : parseInt(val)
        }));
        return {
          id: `group_${groupKey}`,
          fromDatasetId: "raw",
          transform: {
            type: "filter",
            config: { and: filterConds }
          }
        };
      })
    ];

    return {
      rawSource,
      groupField,
      yFields,
      xAxis,
      yAxis,
      y2Axis,
      dataset,
      treeData,
      visibleGroups,
      defaultProps: {
        children: "children",
        label: "label"
      },
      visibleFields: yFields.map(f => f.field)
    };
  },

  methods: {
    handleCheckChange() {
      const keys = this.$refs.groupTree.getCheckedKeys(true);
      this.visibleGroups = keys.filter(k => {
        if (k === "all") return false;
        if (this.groupField.length === 1) {
          return true;
        } else {
          return k.includes("_");
        }
      });
    }
  },

  computed: {
    chartOptions() {
      // 是否需要右轴：yFields 中存在 yAxisIndex = 1 且字段已可见
      const needRightAxis = this.yFields.some(
        f => f.yAxisIndex === 1 && this.visibleFields.includes(f.field)
      );

      const yAxisConfig = [this.yAxis];
      if (needRightAxis) yAxisConfig.push(this.y2Axis);

      const series = [];
      this.visibleGroups.forEach(groupKey => {
        this.yFields.forEach(({ field, label, color, yAxisIndex }) => {
          if (this.visibleFields.includes(field)) {
            series.push({
              type: "line",
              name: `${groupKey} - ${label}`,
              datasetId: `group_${groupKey}`,
              encode: { x: "time", y: field },
              lineStyle: { color },
              itemStyle: { color },
              yAxisIndex
            });
          }
        });
      });

      return {
        tooltip: {},
        legend: { top: "top" },
        xAxis: this.xAxis,
        yAxis: yAxisConfig,
        dataset: this.dataset,
        series
      };
    }
  }
};
</script>

<style>
.custom-tree-node {
  user-select: none;
}
</style>
