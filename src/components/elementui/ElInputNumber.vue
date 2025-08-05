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
    }
  },
  mounted() {
    console.log("DemoComponent1 mounted");
  },
  destroyed() {
    console.log("DemoComponent1 destroyed");
  }
};
</script>
<!--  lang="scss": 使用SCSS预处理语言，不是纯CSS语言 -->
<!--  scoped: 只对当前的组件有效 -->
<style lang="scss" scoped>
.div-cla {
  background-color: green;

}
</style>
