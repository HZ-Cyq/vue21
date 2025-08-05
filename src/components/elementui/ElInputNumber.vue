<template>
  <div class="div-cla">
    次数：<el-input-number v-model="num"></el-input-number>
  </div>
</template>

<script>
export default {
  name: "DemoComponent1",
  data() {
    return {
      num: 1,
    };
  },
  mounted() {
    console.log("DemoComponent1 mounted");
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
  ::v-deep(.el-input-number__increase)
   {
    width: 0 !important;
    padding: 0 !important;
    margin: 0 !important;
    display: none;
  }
  // 调小输入框左边两边的padding
  ::v-deep(.el-input__inner) {
    padding-left:10px !important;
    padding-right: 10px !important;
  }

  // 👉 选中整个 el-input-number 边框容器
  // ::v-deep(.el-input-number) {
  //   background-color: green;
  //   border-color: green;
  // }
}
</style>
