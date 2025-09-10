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
                    ["product", "2023", "2024", "2025"],
                    ["咖啡", 10, 20, 30],
                    ["奶茶", 20, 30, 40],
                    ["奶", 1, 2, 3]
                ]
            },
            xAxis: { type: "category", name: "品类" },
            yAxis: { name:"销量"},
            series:[{type:'bar'}, {type:'bar'},{type:'bar'}],
            legend:{},
            tooltip:{}
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