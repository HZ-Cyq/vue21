<template>
    <div style="padding:20px">
        <el-button type="primary" @click="toggleTable">
            {{ showTable ? '关闭表格' : '打开表格' }}
        </el-button>

        <div v-if="showTable" style="margin-top: 20px;">
            <el-table :data="tableData" border style="width: 100%">
                <el-table-column type="index" label="序号" width="80"></el-table-column>
                <el-table-column prop="rank" label="威胁排序" width="150"></el-table-column>
                <el-table-column prop="name" label="导弹名称"></el-table-column>
            </el-table>
        </div>
    </div>
</template>

<script>
export default {
    name: "TableDemo",
    data() {
        return {
            showTable: false,
            tableData: []
        }
    },
    mounted() {
        // // 注册 Vue 更新回调
        // dataCenter.registerCallback((array) => {
        //     this.tableData = array
        // })

        // // 模拟“每帧更新”
        // this.loop = setInterval(() => {
        //     dataCenter.update(this.showTable)
        // }, 500) // 每0.5秒执行一次模拟帧
    },
    beforeDestroy() {
        clearInterval(this.loop)
    },
    methods: {
        // toggleTable() {
        //     this.showTable = !this.showTable

        //     // 如果打开表格，立即同步一次
        //     if (this.showTable) {
        //         dataCenter.update(true)
        //     }
        // }
    }
}
</script>