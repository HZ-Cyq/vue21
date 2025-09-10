<template>
    <div class="echart-container">
        <div class="echart" ref="echart"></div>
    </div>
</template>
<script>

import * as echarts from 'echarts';
export default {
    name: "MyTest",
    data() {
        return {
            lineEchart: null,
        }
    },
    mounted() {
        this.lineEchart = echarts.init(this.$refs.echart);
        console.log("MyTest mounted");
        let option = {
            title: {"text": "这是标题"},
            tooltip: {trigger: "axis"},
            xAxis: {type:"category", name:'日期', data: ['周一','周二','周三','周四','周五'], },
            yAxis: {type:'value', name:'销量与人数'},
            series: [
                {
                    type:'line',
                    name:'销量',
                    data:[1,2,3,2,3],
                    smooth:true,
                },
                {
                    type:'bar',
                    name:'人数',
                    data:[1,2,4,2,3],
                    smooth:true,
                }
            ],
            legend: {"data":['人数','销量']},
        };
        this.lineEchart.setOption(option);
    }
}
</script>
<style scoped>
.echart-container {
    height: 100%;
    width: 100%;
}
.echart {
    height: 100%;
    width: 100%;
}
</style>