<template>
    <div>
      <h2>我是 DemoComponent1</h2>
      <p>这是一个动态加载的 Vue 组件</p>
    </div>
  </template>
  
  <script>

  export default {
    name: "DemoComponent1",
    mounted() {
      console.log("DemoComponent1 mounted");
    },
    destroyed() {
      console.log("DemoComponent1 destroyed");
    }
  };
  </script>
  