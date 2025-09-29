<template>
    <div id="app">
      <h2>批量 蓝色 → 自定义颜色 映射工具</h2>
  
      <!-- 批量上传图片 -->
      <input type="file" multiple @change="onFilesChange" />
      <br /><br />
  
      <!-- 选择目标颜色 -->
      <label>目标颜色：</label>
      <input type="color" v-model="targetColor" />
      <span>{{ targetColor }}</span>
  
      <!-- 图片预览 -->
      <div class="preview" v-if="images.length">
        <div v-for="(img, index) in images" :key="index">
          <h3>{{ img.name }}</h3>
          <!-- 原图 Canvas -->
          <canvas ref="originalCanvas"></canvas>
          <!-- 转换后的 Canvas -->
          <canvas ref="convertedCanvas"></canvas>
        </div>
      </div>
  
      <!-- 批量转换按钮 -->
      <button @click="convertAll">批量转换并下载 ZIP</button>
    </div>
  </template>
  
  <script>
  import JSZip from "jszip"; // 用于在浏览器端生成 zip 文件
  
  export default {
    name: "ChangePngColor",
    data() {
      return {
        images: [],       // 存储上传的图片信息 [{name, image}]
        targetColor: "#ff0000", // 默认目标颜色红色
      };
    },
    methods: {
      /**
       * 文件上传处理
       * @param {Event} e - input change 事件
       */
      onFilesChange(e) {
        this.images = [];
        const files = e.target.files;
        if (!files.length) return;
        for(let file of files) {
          const reader = new FileReader(); // 读取文件为 DataURL
          reader.onload = (event) => {
            const img = new Image(); // 创建 Image 对象
            img.onload = () => {
              // 将图片对象加入 images 数组
              this.images.push({ name: file.name, image: img });
  
              // 等 DOM 渲染完后设置 canvas 宽高和绘制原图
              this.$nextTick(() => {
                const index = this.images.length - 1;
  
                // 原图 Canvas 设置
                const origCanvas = this.$refs.originalCanvas[index];
                origCanvas.width = img.width;
                origCanvas.height = img.height;
                origCanvas.getContext("2d").drawImage(img, 0, 0);
  
                // 转换 Canvas 设置宽高，但先不绘制
                const convCanvas = this.$refs.convertedCanvas[index];
                convCanvas.width = img.width;
                convCanvas.height = img.height;
              });
            };
            img.src = event.target.result; // 设置图片源
          };
          reader.readAsDataURL(file); // 读取文件
        }
        // Array.from(files).forEach((file) => {
        // });
      },
  
      /**
       * 十六进制颜色转 RGB 对象
       * @param {string} hex - 十六进制颜色，如 #ff5027
       * @returns {Object} {r, g, b}
       */
      hexToRgb(hex) {
        hex = hex.replace("#", "");
        if (hex.length === 3) hex = hex.split("").map(c => c + c).join("");
        const num = parseInt(hex, 16);
        return {
          r: (num >> 16) & 255,
          g: (num >> 8) & 255,
          b: num & 255
        };
      },
  
      /**
       * 将图片中的蓝色或偏蓝色转换成目标颜色
       * @param {Image} img - 原始图片对象
       * @param {HTMLCanvasElement} canvas - 转换后的 Canvas
       * @param {Object} target - RGB 目标颜色 {r, g, b}
       */
      processCanvas(img, canvas, target) {
        const ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0);
  
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const data = imageData.data;
  
        for (let i = 0; i < data.length; i += 4) {
          const r = data[i];
          const g = data[i + 1];
          const b = data[i + 2];
  
          // 蓝色占主导（b最大）
          if (b > r && b > g) {
            const factor = b / 255; // 保留蓝色明暗程度
            data[i] = Math.round(target.r * factor); // 替换 R
            data[i + 1] = Math.round(target.g * factor); // 替换 G
            data[i + 2] = Math.round(target.b * factor); // 替换 B
          }
          // 其他颜色保持不变
        }
  
        ctx.putImageData(imageData, 0, 0);
      },
  
      /**
       * 批量处理所有图片并打包下载 zip
       */
      async convertAll() {
        if (!this.images.length) {
          alert("请先上传图片");
          return;
        }
  
        const target = this.hexToRgb(this.targetColor);
        const zip = new JSZip();
  
        for (let index = 0; index < this.images.length; index++) {
          const item = this.images[index];
  
          // 确保 DOM 已渲染
          await this.$nextTick();
  
          const canvas = this.$refs.convertedCanvas[index];
          if (!canvas) continue;
  
          // 处理蓝色转换
          this.processCanvas(item.image, canvas, target);
  
          // 转换 Canvas 为 blob
          const blob = await new Promise(resolve => canvas.toBlob(resolve, "image/png"));
          zip.file(item.name, blob); // 保留原始文件名
        }
  
        // 生成 ZIP 并用浏览器下载
        const content = await zip.generateAsync({ type: "blob" });
        const url = URL.createObjectURL(content);
        const a = document.createElement("a");
        a.href = url;
        a.download = "converted_images.zip";
        a.click();
        URL.revokeObjectURL(url);
      }
    }
  };
  </script>
  
  <style>
  #app { text-align: center; margin-top: 20px; }
  
  /* 预览区域样式 */
  .preview { display: flex; flex-wrap: wrap; justify-content: center; margin: 20px 0; }
  .preview div { margin: 10px; }
  
  /* canvas 样式 */
  canvas { max-width: 200px; border: 1px solid #ccc; display: block; margin: 5px auto; }
  </style>
  