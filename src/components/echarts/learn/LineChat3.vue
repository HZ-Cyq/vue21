<template>
    <div style="width: 800px; height: 500px">
        <v-chart :option="chartOptions" autoresize />
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
        ]

        const header = rawSource[0];
        const rows = rawSource.slice(1);

        // 提取唯一的 TrajID
        const trajIdSet = new Set(rows.map(r => r[header.indexOf("TrajID")]));
        const uniqueTrajIds = Array.from(trajIdSet);

        // 构建 dataset 配置
        const dataset = [
            {
                id: "raw",
                source: rawSource
            },
            ...uniqueTrajIds.map(id => ({
                id: `traj${id}`,
                fromDatasetId: "raw",
                transform: {
                    type: "filter",
                    config: { dimension: "TrajID", eq: id }
                }
            }))
        ];

        // 构建 series 配置（画纬度 vs 时间图）
        const series = uniqueTrajIds.map(id => ({
            type: "line",
            name: `Traj ${id}`,
            datasetId: `traj${id}`,
            encode: {
                x: "time",
                y: "Latitude"
            }
        }));

        return {
            chartOptions: {
                tooltip: {},
                legend: { top: "top" },
                xAxis: { type: "value", name: "时间" },
                yAxis: { type: "value", name: "纬度" },
                dataset,
                series
            }
        };
    }
};
</script>