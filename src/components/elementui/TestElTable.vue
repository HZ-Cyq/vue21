<template>
  <div>
    <!-- {{ tableData }} -->
    <el-table :data="tableData" stripe style="width: 100%">
      <el-table-column prop="date" label="日期" width="180">
      </el-table-column>
      <el-table-column prop="name" label="姓名" width="180">
      </el-table-column>
      <el-table-column prop="address" label="地址">
      </el-table-column>
    </el-table>
    <el-pagination
      layout="prev, pager, next"
      :total="1000"
    >
    </el-pagination>
  </div>
</template>

<script>
export default {
  name: "ElTableTest",
  data() {
    return {
      curPage: 1,
      pageSize: 5,
      total: 1,
      tableData: [],
      // 模仿数据库中的数据
      totalTableData: [{
        date: '2016-05-01',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      },
       {
        date: '2016-05-02',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1517 弄'
      }, {
        date: '2016-05-03',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1519 弄'
      }, {
        date: '2016-05-04',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-05',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-06',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-07',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-08',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-09',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-10',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-11',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-12',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-13',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-14',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
       {
        date: '2016-05-15',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      },
    ]
    }
  },

  computed: {
    // 获取当前页的数据
    getCurPageData() {
      let start = (this.curPage - 1) * this.pageSize;
      let end = start + this.pageSize;
      return this.totalTableData.slice(start, end);
    }
  },
  methods: {
    // 获取总页数
    getTotalPage() {
      return Math.ceil(this.totalTableData.length / this.pageSize);
    },

    /**
     * 
     * @param pageSize 获取服务器数据，根据每页数量和当前页
     * @param curPage 
     */
    getServerData(pageSize, curPage) {
      // 总行数
      let totalRow = this.totalTableData.length;
      // 表格内容
      let tableData = this.totalTableData.splice((curPage - 1) * pageSize, (curPage - 1) * pageSize + pageSize);
      let re = {totalRow, tableData};
      return re;
    }
  },
  mounted() {
      let re = this.getServerData(this.pageSize, this.curPage);
      this.tableData = re.tableData;
  },
  destroyed() {
    console.log("DemoComponent1 destroyed");
  }
};
</script>

<style lang="scss" scoped>
.div-cla {
  background-color: green;

  // 👉 深度作用：选中 el-input-number 内部的 input
  ::v-deep(.el-input__inner) {
    background-color: transparent;
    color: white; // 可选：字体颜色
  }

  // 隐藏增加和减少按钮
  ::v-deep(.el-input-number__decrease),
  ::v-deep(.el-input-number__increase) {
    width: 0 !important;
    padding: 0 !important;
    margin: 0 !important;
    display: none;
  }

  // 调小输入框左右两边的padding
  ::v-deep(.el-input__inner) {
    padding-left: 10px !important;
    padding-right: 10px !important;
  }

  // 👉 选中整个 el-input-number 边框容器
  // ::v-deep(.el-input-number) {
  //   background-color: green;
  //   border-color: green;
  // }
}
</style>
