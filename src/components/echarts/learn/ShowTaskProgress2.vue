<template>
    <div class="progress-grid">
      <h2>任务进度图</h2>
      <div class="progress-info">
        已完成 {{ completedTasks.length }} / {{ totalTasks }} 个任务
      </div>
      <div
        class="grid"
        :style="{ gridTemplateColumns: `repeat(${cols}, 40px)` }"
      >
        <div
          v-for="(task, index) in tasks"
          :key="index"
          class="cell"
          :class="{ completed: isCompleted(task) }"
        >
          {{ task }}
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "ShowTaskProgress2",
    props: {
      rows: {
        type: Number,
        default: 7
      },
      cols: {
        type: Number,
        default: 7
      },
      completedTasks: {
        type: Array,
        default: () => []
      }
    },
    computed: {
      // 所有任务
      tasks() {
        let list = [];
        for (let i = 1; i <= this.rows; i++) {
          for (let j = 1; j <= this.cols; j++) {
            list.push(`${i}_${j}`);
          }
        }
        return list;
      },
      totalTasks() {
        return this.rows * this.cols;
      }
    },
    methods: {
      isCompleted(task) {
        return this.completedTasks.includes(task);
      }
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
  