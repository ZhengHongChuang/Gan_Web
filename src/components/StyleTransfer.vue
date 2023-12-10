<template>
  <div>
    <el-container>
      <el-header class="header">
        <h1>风格转换</h1>
      </el-header>
      <div class="buttons">
        <el-button @click="selectLocalImage" type="primary">选择</el-button>
        <el-button @click="submitImage" type="success">转换</el-button>
      </div>
      
      <el-main>
        <img :src="originalImageUrl" alt="Original Image" v-if="originalImageUrl" />
        <img :src="stylizedImageUrl" alt="Stylized Image" v-if="stylizedImageUrl" />
      </el-main>
    </el-container>
    <input ref="fileInput" type="file" style="display: none" @change="handleFileChange" />
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      originalImageUrl: null,
      stylizedImageUrl: null,
    };
  },
  methods: {
    selectLocalImage() {
      this.$refs.fileInput.click();
    },
    handleFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.stylizedImageUrl = null;
        this.originalImageUrl = URL.createObjectURL(file);
      }
    },
    async submitImage() {
      if (!this.originalImageUrl) {
        alert("请先选择本地图片");
        return;
      }

      this.stylizedImageUrl = null; 

      const formData = new FormData();
      const file = await this.urltoFile(this.originalImageUrl);
      formData.append('image', file);

      try {
        const response = await axios.post('http://10.180.193.13:7000/styleTransfer', formData, {
          responseType: 'arraybuffer',
        });

        const blob = new Blob([response.data], { type: 'image/jpeg' });
        const imageUrl = URL.createObjectURL(blob);
        this.stylizedImageUrl = imageUrl;
        console.log(response.data);
      } catch (error) {
        console.error('错误:', error);
      }
    },
    async urltoFile(url) {
      const response = await fetch(url);
      const blob = await response.blob();
      return new File([blob], 'image.png', { type: 'image/png' });
    },
  },
};
</script>

<style>
img {
  max-width: 100%;
  height: auto;
  margin-top: 20px;
}

.header {
  margin-top: 0px;
}

.buttons {
  margin-top: 0px;
}
.el-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
</style>
