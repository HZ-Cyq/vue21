<template>
  <div id="app" class="layout">
    <!-- 左侧菜单 -->
    <div class="sidebar">
      <HoverTreeMenu :nodes="menuData" @component-selected="loadComponent" />
    </div>

    <div class="main">
      <!-- 动态组件面板 -->
      <div class="plugin-panel" v-if="currentComponent">
        <component :is="currentComponent.component" :key="currentComponent.key" />
      </div>
    </div>
  </div>
</template>

<script>
import HoverTreeMenu from "@/components/hovertreemenus/HoverTreeMenu.vue";
export default {
  name: 'App',
  components: {
    HoverTreeMenu
  },
  data() {
    return {
      menuData: [
        {
          title: "测试菜单",
          subMenu: [
            { title: "DemoComponent1", componentName: "DemoComponent1" },
            { title: "DemoComponent2", componentName: "DemoComponent2" },
            { title: "MyTest", componentName: "MyTest" },
          ],
        },
        {
          title: "element-ui",
          subMenu: [
            { title: "TestElInputNumber", componentName: "TestElInputNumber" },
            { title: "TestElTable", componentName: "TestElTable" },
            { title: "TestElTable2", componentName: "TestElTable2" },
            { title: "TestElInput", componentName: "TestElInput" }
          ]
        },
        {
          title: "资源",
          subMenu: [
            { title: "ShowImg", componentName: "ShowImg" },
            { title: "ShowGlb", componentName: "GlbViewer" },
            { title: "ChangePngColor", componentName: "ChangePngColor" },
          ],
        },
        {
          title: "颜色",
          subMenu: [
            { title: "色版", componentName: "ColorLegendExample" },
          ],
        },
        {
          title: "echart字段",
          subMenu: [
            { title: "dataset字段", componentName: "TestEchartDataset" },
            { title: "tooltip字段", componentName: "TestEchartTooltip" },
            { title: "legend字段", componentName: "TestEchartLegend" },
            { title: "trigger字段-axis", componentName: "TestAxisTrigger" },
            { title: "trigger字段-item", componentName: "TestItemTrigger" },
            { title: "title字段", componentName: "TestEchartTitle" },
          ]
        },
        {
          title: "echarts2",
          subMenu: [
            { title: "二维进度图", componentName: "ShowTaskProgress"},
            { title: "双Y轴折线图+dataset+transform", componentName: "DoubleYAxiChartWithDatasetAndTransform" },
            { title: "双Y轴折线图", componentName: "DoubleYAxiChart" },
            { title: "动态折线图", componentName: "DynamicChart" },
            { title: "按TrajID分组", componentName: "LineChart23" },
            { title: "按行映射还是按列映射", componentName: "SeriesLayout" },
            { title: "X轴的type", componentName: "XaxisType" },
          ]
        },
        {
          title: "echarts",
          subMenu: [
            { title: "散点图", componentName: "TestBasicScatterChart" },
            { title: "带筛选的折线图2", componentName: "SelectedChart3" },
            { title: "折线图", componentName: "LineChart" },
            { title: "柱状图", componentName: "BarChart" },
            { title: "饼状图", componentName: "PieChart" },
            { title: "折线图2", componentName: "LineChart2" },
            { title: "折线图3", componentName: "LineChart3" },
            { title: "折线图4", componentName: "LineChart4" },
            { title: "查询界面", componentName: "LineChart5" },
            { title: "查询界面带递归", componentName: "ConditionGroup" },
            { title: "查询界面带递归2", componentName: "ConditionGroup2" },
            { title: "带筛选的折线图", componentName: "SelectedChart" },
            { title: "带筛选的折线图2", componentName: "SelectedChart2" },
          ],
        },

      ],
      currentComponent: null,
    }
  },
  methods: {
    async loadComponent(name) {
      const componentMap = {
        DemoComponent1: () => import("@/components/hovertreemenus/DemoComponent1.vue"),
        DemoComponent2: () => import("@/components/hovertreemenus/DemoComponent2.vue"),
        ShowImg: () => import("@/components/resourceshow/ShowImg.vue"),
        GlbViewer: () => import("@/components/resourceshow/GlbViewer.vue"),
        ChangePngColor: () => import("@/components/resourceexchange/ChangePngColor.vue"),
        ColorLegendExample: () => import("@/components/color/ColorLegendExample.vue"),
        LineChart: () => import("@/components/echarts/LineChart.vue"),
        BarChart: () => import("@/components/echarts/BarChart.vue"),
        PieChart: () => import("@/components/echarts/PieChart.vue"),
        LineChart2: () => import("@/components/echarts/LineChart2.vue"),
        LineChart3: () => import("@/components/echarts/LineChart3.vue"),
        LineChart4: () => import("@/components/echarts/LineChart4.vue"),
        LineChart5: () => import("@/components/echarts/LineChart5.vue"),
        ConditionGroup: () => import("@/components/echarts/ConditionGroup.vue"),
        ConditionGroup2: () => import("@/components/echarts/ConditionGroup2.vue"),
        SelectedChart: () => import("@/components/echarts/SelectedChart.vue"),
        SelectedChart2: () => import("@/components/echarts/SelectedChart2.vue"),
        SelectedChart3: () => import("@/components/echarts/SelectedChart3.vue"),
        SeriesLayout: () => import("@/components/echarts/learn/SeriesLayout.vue"),
        XaxisType: () => import("@/components/echarts/learn/XaxisType.vue"),
        LineChart23: () => import("@/components/echarts/learn/LineChat3.vue"),
        LineChart44: () => import("@/components/echarts/learn/LineChart4.vue"),
        DynamicChart: () => import("@/components/echarts/learn/DynamicChart.vue"),
        ShowTaskProgress: () => import("@/components/echarts/learn/ShowTaskProgress.vue"),
        
        TestAxisTrigger: () => import("@/components/echarts/learn/field/trigger/TestAxisTrigger.vue"),
        TestItemTrigger: () => import("@/components/echarts/learn/field/trigger/TestItemTrigger.vue"),
        TestEchartTitle: () => import("@/components/echarts/learn/field/title/TestEchartTitle.vue"),
        TestEchartLegend: () => import("@/components/echarts/learn/field/legend/TestEchartLegend.vue"),
        TestEchartTooltip: () => import("@/components/echarts/learn/field/tooltip/TestEchartTooltip.vue"),
        TestEchartDataset: () => import("@/components/echarts/learn/field/dataset/TestEchartDataset.vue"),
        
        DoubleYAxiChart: () => import("@/components/echarts/learn/type/DoubleYAxiChart.vue"),
        DoubleYAxiChartWithDatasetAndTransform: () => import("@/components/echarts/learn/type/DoubleYAxisChartWithDatasetAndTransform.vue"),

        TestElInputNumber: () => import("@/components/elementui/TestElInputNumber.vue"),
        TestElTable: () => import("@/components/elementui/TestElTable.vue"),
        TestElTable2: () => import("@/components/elementui/TestElTable2.vue"),
        TestBasicScatterChart: () => import("@/components/echarts/TestBasicScatterChart.vue"),
        TestElInput: () => import("@/components/elementui/testelinput/TestElInput.vue"),
        MyTest: () => import("@/components/echarts/learn/MyTest.vue")
      };

      try {
        if (!componentMap[name]) throw new Error("组件未注册：" + name);
        const mod = await componentMap[name]();
        this.currentComponent = {
          component: mod.default,
          key: name + "-" + Date.now(),
        };
      } catch (e) {
        alert("加载组件失败：" + name);
        console.error(e);
      }
    },
  }
}
</script>

<style scoped>
.layout {
  display: flex;
  height: 100vh;
  overflow: hidden;
}

.sidebar {
  width: 20%;
  background-color: #2c3e50;
  /* overflow-y: auto; */
}

.main {
  flex: 1;
  /* position: relative; */
  overflow: auto;
}

.plugin-panel {
  position: absolute;
  top: 20px;
  right: 20px;
  width: 75%;
  height: 100vh;
  background: white;
  border: 1px solid #ccc;
  padding: 10px;
  /* z-index: 999; */
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}
</style>
