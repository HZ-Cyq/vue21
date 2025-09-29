<template>
  <div>
    <ShowTaskProgress2 :rows="rows" :cols="cols" :completedTasks="completedTasks"></ShowTaskProgress2>
  </div>
</template>

<script>
import ShowTaskProgress2 from './ShowTaskProgress2.vue';

export default {
  name: "ShowTaskProgress",
  data() {
    return {
      rows: 8,
      cols: 8,
      // 已完成的任务列表
      completedTasks: ["1_1", "1_2", "2_1", "3_2", "4_5"]
    };
  },
  components: {ShowTaskProgress2},
  mounted() {
    console.log("ShowTaskProgress mounted over");
  }
};
</script>

<style scoped>
.progress-grid {
  text-align: center;
  font-family: Arial, sans-serif;
}

.progress-info {
  margin: 10px 0;
  font-weight: bold;
}

.grid {
  display: grid;
  grid-template-columns: repeat(7, 40px); /* 每行7个 */
  gap: 5px;
  justify-content: center;
}

.cell {
  width: 40px;
  height: 40px;
  line-height: 40px;
  border: 1px solid #aaa;
  font-size: 12px;
  color: #555;
  background-color: #eee;
  border-radius: 4px;
}

.cell.completed {
  background-color: #4caf50;
  color: white;
  font-weight: bold;
}
</style>
