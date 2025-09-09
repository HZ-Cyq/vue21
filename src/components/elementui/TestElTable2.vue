<template>
    <div class="layout">
      <div class="top-box">
        <el-input v-model="aa"></el-input>
        {{ aa }}
      </div>
      <!-- 内容区（表格可滚动） -->
      <div class="content">
        <el-table :data="tableData" border style="width: 100%">
          <el-table-column prop="name" label="姓名" />
          <el-table-column prop="age" label="年龄" />
        </el-table>
      </div>
  
      <!-- 底部分页 -->
      <div class="footer">
        <el-pagination
          layout="prev, pager, next, jumper"
          :total="100"
          :page-size="10"
          @current-change="handlePageChange"
        />
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "TableWithPagination",
    data() {
      return {
        tableData: [],
        aa: "11",
      };
    },
    created() {
      this.loadData(1);
    },
    methods: {
      loadData(page) {
        this.tableData = Array.from({ length: 10 }, (_, i) => ({
          name: `用户 ${ (page - 1) * 10 + i + 1 }`,
          age: Math.floor(Math.random() * 40) + 20
        }));
      },
      handlePageChange(page) {
        this.loadData(page);
      }
    }
  };
  </script>
  
  <style scoped>
  .top-box {
    color: red;
  }
  .layout {
    display: flex;
    flex-direction: column;
    width: 800px;   /* 固定宽度 */
    height: 600px;  /* 固定高度 */
    border: 1px solid #ddd;
    box-sizing: border-box;
  }
  
  .content {
    flex: 1;             /* 占据剩余空间 */
    overflow-y: auto;    /* 内容超出时滚动 */
    padding: 10px;
    box-sizing: border-box;
  }
  
  .footer {
    border-top: 1px solid #eee;
    padding: 10px;
    text-align: center;
    background: #fff;
  }
  </style>
  