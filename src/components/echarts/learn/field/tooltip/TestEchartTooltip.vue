<template>
    <div>
        <div class="echart" ref="chartRef"></div>
    </div>
</template>
<script>
import * as echarts from 'echarts'
export default {
    mounted() {
        let chart = echarts.init(this.$refs.chartRef);
        let option = {
            xAxis: {type:"category",name:"日期",data:['周一','周二','周三','周四']},
            yAxis: {type:'value',name:"销量"},
            series: [
                {type:'line', name:'销量', data:[1,4,2,4]}
            ],
            tooltip: {}
        };
        chart.setOption(option);
    }
}
</script>
<style scoped>
.echart {
    height: 400px;
    width: 100%;
}
</style>