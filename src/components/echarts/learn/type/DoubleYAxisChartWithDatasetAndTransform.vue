<template>
    <div>
        <div class="echart" ref="echartRef"></div>
    </div>
</template>
<script>

import * as echarts from "echarts"

export default {
    data() {
        return {};
    },
    mounted() {
        let echart = echarts.init(this.$refs.echartRef);
        let option = {
            dataset: {
                source: [
                    ['日期', '销量', '人数', '营业额', '利润'],
                    ['周一', 1, 10, 210, 100],
                    ['周二', 2, 20, 220, 110],
                    ['周三', 3, 30, 230, 130],
                    ['周四', 2, 22, 222, 102]
                ]
            },
            xAxis: { type: "category", name: "日期" },
            yAxis: [{ type:"value", name: "销量与人数"}, {type:"value", name:"营业额与利润"}],
            series: [{ type: 'line', yAxisIndex: 0}, { type: 'line', yAxisIndex:0 }, { type: 'line', yAxisIndex: 1}, {type:'line', yAxisIndex: 1}],
            legend: {},
            tooltip: {}
        };
        echart.setOption(option);
        console.log("TestEchartDataset mounted over");
    }
}
</script>
<style scoped>
.echart {
    height: 400px;
    width: 100%;
}
</style>