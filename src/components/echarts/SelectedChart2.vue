<template>
  <div>
    <el-checkbox-group v-model="checkedCities">
      <el-checkbox label="北京"></el-checkbox>
      <el-checkbox label="上海"></el-checkbox>
      <el-checkbox label="广州"></el-checkbox>
      <el-checkbox label="深圳"></el-checkbox>
    </el-checkbox-group>
  </div>
</template>

<script>
export default {
  data() {
    return {
      checkedCities: ['上海'], // 默认选中上海
    }
  }
}
</script>
