<template>
    <div>
      <el-input
        type="textarea"
        v-model="jsonInput"
        :rows="15"
        placeholder="请输入 JSON 字符串"
        @blur="formatJson"
      />
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        jsonInput: ''
      }
    },
    methods: {
      formatJson() {
        if (!this.jsonInput) return
        try {
          // 先解析再格式化
          const obj = JSON.parse(this.jsonInput)
          this.jsonInput = JSON.stringify(obj, null, 2)
        } catch (e) {
          this.$message.error("JSON 格式错误，请检查输入")
        }
      }
    }
  }
  </script>
  