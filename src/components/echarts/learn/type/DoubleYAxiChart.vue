<template>
    <div>
        <div class="echart" ref="echartRef"></div>
    </div>
</template>
<script>
import * as echarts from 'echarts'
export default {
    data() {
        return {};
    },
    mounted() {
        console.log("DoubleYAixChart.vue init over");
        let echart = echarts.init(this.$refs.echartRef);
        let option = {
            xAxis: {type:'category', name: "日期", data:['周一','周二','周三','周四']},
            yAxis: [{type: 'value', name: '人数和销量'},{ type:'value',name:"营业额和利润"}],
            series: [
                {type:'line', name:'销量', data:[1,2,3,2], yAxisIndex:0},
                {type:'line', name:'人数', data:[10,20,30,22], yAxisIndex:0},
                {type:'line', name:'营业额', data:[210,220,230,222],yAxisIndex:1},
                {type:'line', name:'利润', data:[100,110,130,102],yAxisIndex:1}
            ],
            tooltip:{},
            legend:{}
        };
        echart.setOption(option);
    }
}
</script>
<style scope>
.echart {
    height: 400px;
    width: 100%;
}
</style>