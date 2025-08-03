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
      <strong>分组选择（{{ groupField.join(', ') }}）：</strong>
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
      [1, 2, 1, 1, 1, 0],
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

    const groupField = ["TrajID", "Gid"];

    const yFields = [
      { field: "Latitude", label: "纬度", color: "#5470C6" },
      { field: "Longitude", label: "经度", color: "#91CC75" }
    ];

    const xAxis = { type: "value", name: "时间(s)" };
    const yAxis = { type: "value", name: "数值(度)" };

    const header = rawSource[0];
    const rows = rawSource.slice(1);

    const trajIdIndex = header.indexOf("TrajID");
    const gidIndex = header.indexOf("Gid");

    // 构建树形数据：先分组TrajID，再分组Gid
    const treeDataMap = new Map();

    rows.forEach(row => {
      const trajId = row[trajIdIndex];
      const gid = row[gidIndex];
      if (!treeDataMap.has(trajId)) {
        treeDataMap.set(trajId, new Map());
      }
      const gidMap = treeDataMap.get(trajId);
      if (!gidMap.has(gid)) {
        gidMap.set(gid, true);
      }
    });

    // 转成 Element Tree 需要的数组结构
    const treeData = [];
    treeDataMap.forEach((gidMap, trajId) => {
      const children = [];
      gidMap.forEach((_, gid) => {
        children.push({
          id: `${trajId}_${gid}`,
          label: `Gid: ${gid}`
        });
      });
      treeData.push({
        id: `${trajId}`,
        label: `TrajID: ${trajId}`,
        children
      });
    });

    // 初始化默认选中全部子节点
    // 这里默认只选子节点(即每条折线)，父节点全选会自动触发子节点全选
    const visibleGroups = [];
    treeData.forEach(trajNode => {
      trajNode.children.forEach(gidNode => {
        visibleGroups.push(gidNode.id);
      });
    });

    // dataset 用于echarts过滤
    const dataset = [
      { id: "raw", source: rawSource },
      ...visibleGroups.map(groupKey => {
        const [trajId, gid] = groupKey.split("_");
        return {
          id: `group_${groupKey}`,
          fromDatasetId: "raw",
          transform: {
            type: "filter",
            config: {
              and: [
                { dimension: "TrajID", eq: parseInt(trajId) },
                { dimension: "Gid", eq: parseInt(gid) }
              ]
            }
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
      this.visibleGroups = this.$refs.groupTree.getCheckedKeys();
    }
  },

  computed: {
    chartOptions() {
      const series = [];
      this.visibleGroups.forEach(groupKey => {
        this.yFields.forEach(({ field, label, color }) => {
          if (this.visibleFields.includes(field)) {
            series.push({
              type: "line",
              name: `${groupKey} - ${label}`,
              datasetId: `group_${groupKey}`,
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

<style>
.custom-tree-node {
  user-select: none;
}
</style>
