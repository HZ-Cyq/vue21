<template>
    <div id="app">
      <h2>批量 蓝色 → 自定义颜色 映射工具</h2>
  
      <input type="file" multiple @change="onFilesChange" />
      <br /><br />
  
      <label>目标颜色：</label>
      <input type="color" v-model="targetColor" />
      <span>{{ targetColor }}</span>
  
      <div class="preview" v-if="images.length">
        <div v-for="(img, index) in images" :key="index">
          <h3>{{ img.name }}</h3>
          <canvas ref="originalCanvas"></canvas>
          <canvas ref="convertedCanvas"></canvas>
        </div>
      </div>
  
      <button @click="convertAll">批量转换并下载 ZIP</button>
    </div>
  </template>
  
  <script>
  import JSZip from "jszip";
  
  export default {
    name: "ChangePngColor",
    data() {
      return {
        images: [], // {name, image}
        targetColor: "#ff0000",
      };
    },
    methods: {
      onFilesChange(e) {
        this.images = [];
        const files = e.target.files;
        if (!files.length) return;
  
        Array.from(files).forEach((file) => {
          const reader = new FileReader();
          reader.onload = (event) => {
            const img = new Image();
            img.onload = () => {
              this.images.push({ name: file.name, image: img });
  
              this.$nextTick(() => {
                const index = this.images.length - 1;
  
                // 原图 canvas
                const origCanvas = this.$refs.originalCanvas[index];
                origCanvas.width = img.width;
                origCanvas.height = img.height;
                origCanvas.getContext("2d").drawImage(img, 0, 0);
  
                // 转换 canvas
                const convCanvas = this.$refs.convertedCanvas[index];
                convCanvas.width = img.width;
                convCanvas.height = img.height;
              });
            };
            img.src = event.target.result;
          };
          reader.readAsDataURL(file);
        });
      },
  
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
  
      processCanvas(img, canvas, target) {
        const ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0);
  
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const data = imageData.data;
  
        for (let i = 0; i < data.length; i += 4) {
          const r = data[i];
          const g = data[i + 1];
          const b = data[i + 2];
  
          // 蓝色占主导
          if (b > r && b > g) {
            const factor = b / 255;
            data[i] = Math.round(target.r * factor);
            data[i + 1] = Math.round(target.g * factor);
            data[i + 2] = Math.round(target.b * factor);
          }
        }
        ctx.putImageData(imageData, 0, 0);
      },
  
      async convertAll() {
        if (!this.images.length) {
          alert("请先上传图片");
          return;
        }
  
        const target = this.hexToRgb(this.targetColor);
        const zip = new JSZip();
  
        for (let index = 0; index < this.images.length; index++) {
          const item = this.images[index];
          await this.$nextTick();
  
          const canvas = this.$refs.convertedCanvas[index];
          if (!canvas) continue;
  
          this.processCanvas(item.image, canvas, target);
  
          const blob = await new Promise(resolve => canvas.toBlob(resolve, "image/png"));
          zip.file(item.name, blob);
        }
  
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
  .preview { display: flex; flex-wrap: wrap; justify-content: center; margin: 20px 0; }
  .preview div { margin: 10px; }
  canvas { max-width: 200px; border: 1px solid #ccc; display: block; margin: 5px auto; }
  </style>
  