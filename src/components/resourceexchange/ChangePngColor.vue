<template>
    <div id="app">
      <h2>批量 蓝色 → 自定义颜色 映射工具</h2>
  
      <!-- el-upload：隐藏文件列表，选择后清空 -->
      <el-upload
        ref="upload"
        :auto-upload="false"
        :on-change="onUploadChange"
        :show-file-list="false"
        multiple
        action="#"
      >
        <el-button type="primary">选择图片文件（替换全部）</el-button>
        <template #tip>
          <div class="el-upload__tip">每次选择将替换之前的所有图片</div>
        </template>
      </el-upload>
  
      <br /><br />
  
      <!-- 选择目标颜色 -->
      <label>目标颜色：</label>
      <input type="color" v-model="targetColor" />
      <span>{{ targetColor }}</span>
  
      <!-- 图片预览 -->
      <div class="preview" v-if="images.length">
        <div v-for="(img, index) in images" :key="index">
          <h3>{{ img.name }}</h3>
          <canvas ref="originalCanvas"></canvas>
          <canvas ref="convertedCanvas"></canvas>
        </div>
      </div>
  
      <!-- 批量转换按钮 -->
      <el-button type="success" @click="convertAll" style="margin-top: 20px;">
        批量转换并下载 ZIP
      </el-button>
    </div>
  </template>
  
  <script>
  import JSZip from 'jszip';
  
  export default {
    name: 'ChangePngColor',
    data() {
      return {
        images: [],       // 存储当前批次的图片
        targetColor: '#00ffff',
        isProcessingFirst: false, // 标记是否正在处理第一批
      };
    },
    methods: {
      /**
       * el-upload change 事件：处理文件选择
       */
      onUploadChange(file, fileList) {
        // ✅ 第一次触发时，清空之前的图片数据
        if (!this.isProcessingFirst) {
          this.isProcessingFirst = true;
          this.images = []; // 清空上一批图片
        }
  
        const isImage = file.raw.type.startsWith('image/');
        if (!isImage) {
          this.$message.warning(`已忽略非图片文件：${file.name}`);
          return;
        }
  
        // 读取当前文件
        this.readFile(file.raw);
  
        // ✅ 如果是最后一个文件，说明本次选择已结束，可以清空 upload 列表
        if (fileList.length === 0 || this.isLastFile(file, fileList)) {
          this.$nextTick(() => {
            this.$refs.upload.clearFiles(); // 清空 UI 显示
            this.isProcessingFirst = false; // 重置标记，为下次选择准备
          });
        }
      },
  
      /**
       * 判断是否是最后一个待处理文件
       */
      isLastFile(currentFile, fileList) {
        // el-upload 的 fileList 是包含当前文件的完整列表
        // 当前文件在列表中的索引
        const currentIndex = fileList.findIndex(f => f.uid === currentFile.uid);
        // 如果是最后一个（或唯一一个）
        return currentIndex === fileList.length - 1;
      },
  
      /**
       * 读取文件并添加到 images
       */
      readFile(file) {
        const reader = new FileReader();
        reader.onload = (event) => {
          const img = new Image();
          img.onload = () => {
            this.images.push({ name: file.name, image: img });
  
            this.$nextTick(() => {
              const index = this.images.length - 1;
              const origCanvas = this.$refs.originalCanvas?.[index];
              const convCanvas = this.$refs.convertedCanvas?.[index];
  
              if (origCanvas) {
                origCanvas.width = img.width;
                origCanvas.height = img.height;
                origCanvas.getContext('2d').drawImage(img, 0, 0);
              }
  
              if (convCanvas) {
                convCanvas.width = img.width;
                convCanvas.height = img.height;
              }
            });
          };
          img.src = event.target.result;
        };
        reader.readAsDataURL(file);
      },
  
      hexToRgb(hex) {
        hex = hex.replace('#', '');
        if (hex.length === 3) {
          hex = hex.split('').map(c => c + c).join('');
        }
        const num = parseInt(hex, 16);
        return {
          r: (num >> 16) & 255,
          g: (num >> 8) & 255,
          b: num & 255,
        };
      },
  
      processCanvas(img, canvas, target) {
        const ctx = canvas.getContext('2d');
        ctx.drawImage(img, 0, 0);
  
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const data = imageData.data;
  
        for (let i = 0; i < data.length; i += 4) {
          const r = data[i];
          const g = data[i + 1];
          const b = data[i + 2];
          // 如果蓝色分量是最大的，就认为是蓝色或偏蓝
          if (b > r && b > g) {
            // 映射：保持亮度比例
            const factor = b / 255; // 蓝色强度比例
            data[i] = Math.round(target.r * factor);
            data[i + 1] = Math.round(target.g * factor);
            data[i + 2] = Math.round(target.b * factor);
          }
        }
  
        ctx.putImageData(imageData, 0, 0);
      },
  
      async convertAll() {
        if (!this.images.length) {
          this.$message.warning('请先上传图片');
          return;
        }
  
        const target = this.hexToRgb(this.targetColor);
        const zip = new JSZip();
  
        await this.$nextTick();
  
        for (let index = 0; index < this.images.length; index++) {
          const item = this.images[index];
          const canvas = this.$refs.convertedCanvas?.[index];
          if (!canvas) continue;
  
          this.processCanvas(item.image, canvas, target);
  
          const blob = await new Promise(resolve => canvas.toBlob(resolve, 'image/png'));
          zip.file(item.name, blob);
        }
  
        const content = await zip.generateAsync({ type: 'blob' });
        const url = URL.createObjectURL(content);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'converted_images.zip';
        a.click();
        URL.revokeObjectURL(url);
  
        this.$message.success('转换完成，ZIP 已下载！');
      },
    },
  };
  </script>
  
  <style>
  #app {
    text-align: center;
    margin-top: 20px;
    font-family: Arial, sans-serif;
  }
  
  .preview {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    margin: 20px 0;
  }
  
  .preview div {
    margin: 10px;
    text-align: center;
  }
  
  canvas {
    max-width: 200px;
    border: 1px solid #ccc;
    display: block;
    margin: 5px auto;
  }
  </style>