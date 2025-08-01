<template>
    <div style="width: 600px; height: 400px">
        <v-chart :option="chartOptions" autoresize />
    </div>
</template>

<script>
export default {

    mounted() {
        console.log("LineChart mounted");
    },

    data() {
        return {
            chartOptions: {
                legend: {},
                tooltip: {},
                dataset: {
                    // 提供一份数据。
                    source: [["time", "TrajID", "Latitude", "Longitude", "Altitude"],
                    [1, 1, 10, 2, 0], [12, 1, 2, 3, 1], [31, 1, 3, 4, 2], [41, 1, 4, 5, 3], [51, 1, 5, 6, 4],
                        // [1, 2, 0, 1, 0], [2, 2, 0, 1, 1], [3, 2, 0, 1, 2], [4, 2, 0, 1, 3], [5, 2, 0, 1, 4],
                        // [1, 3, 0, 10, 0], [2, 3, 0, 11, 1], [3, 3, 0, 11, 2], [4, 3, 0, 11, 3], [5, 3, 0, 11, 4],
                    ]
                },
                // 声明一个 X 轴，类目轴（category）。默认情况下，类目轴对应到 dataset 第一列。
                xAxis: { type: 'value', name: "时间(s)"},
                // 声明一个 Y 轴，数值轴。
                yAxis: {},
                // 声明多个 bar 系列，默认情况下，每个系列会自动对应到 dataset 的每一列。
                series: [
                    { type: 'line' ,name:'TrajID', encode: { x: 'time', y: 'TrajID' }},
                    { type: 'line', name: "维度", encode: { x: 'time', y: 'Latitude' }},
                    { type: 'line', name: "经度", encode: { x: 'time', y: 'Longitude' }}
                ]
            }
        }
    }

}
</script>