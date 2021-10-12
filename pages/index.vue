<template>
  <el-row type="flex" class="poster-container" justify="center">
    <el-col :span="12">
      <div
        id="poster-preview"
        :style="{ cursor: imageUrl ? 'move' : 'default' }"
      >
        <div class="bg"></div>
        <img class="poster-template" src="meetup.png" />
        <div class="poster-content">
          <div class="title">{{ github }}</div>
        </div>
      </div>
    </el-col>
    <el-col
      :span="8"
      class="poster-control"
      v-loading="isDownloading"
      element-loading-text="生成海报中"
    >
      <el-row>
        <h1>Apache APISIX Meetup 邀请函生成器</h1>
      </el-row>
      <el-form>
        <el-form-item label="GitHub">
          <el-input v-model="github" />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="download()"> 生成海报 </el-button>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<script lang="ts">
import Vue from "vue";
// @ts-ignore
import domtoimage from "retina-dom-to-image";

export default Vue.extend({
  data() {
    return {
      github: "",
      title: "",
      date: "",
      time: "",
      location: "",
      imageUrl: null as unknown as string,
      topic: "",
      isKeynote: false,
      avatarInput: null,
      isDownloading: false,
      posterBase64: "",
      qrCodeArr: [],
      qrCodeDesc: { desc1: "", desc2: "" },
    };
  },

  async mounted() {
    const ids = [
      "dickens7",
      "Serendipity96",
      "lianghao208",
      "朱骏杰",
      "boolean",
      "Molio-tan",
      "nic-chen",
      "miss-you",
      "zhendongcmss",
      "tao12345666333",
      "Rakuten",
      "zenozeng",
      "okaybase",
      "Donghui0",
      "Demogorgon314",
      "tokers",
      "nodyang",
      "liuxiran",
      "sober-wang",
      "NothingNodust",
      "paoying",
      "batman-ezio",
      "mousycoder",
      "zhangbing17",
      "lidaohang",
      "Sindweller",
      "starsz",
      "Jaycean",
      "TkClark",
      "codjust",
    ];
    // for (const id of ids) {
    //   this.github = id;
    //   await this.download();
    // }
  },

  methods: {
    async download() {
      return new Promise((resolve, reject) => {
        this.isDownloading = true;
        domtoimage
          .toJpeg(document.getElementById("poster-preview"))
          .then((url: string) => {
            this.posterBase64 = url;
            this.isDownloading = false;
            const link = document.createElement("a");
            link.href = url;
            link.download = this.github + ".jpeg";
            link.click();
            resolve(true);
          });
      });
    },
    handleRemove(file: any, fileList: any) {
      let arrList: any = [];
      fileList.map((file: any) => {
        arrList.push(window.URL.createObjectURL(file.raw));
      });
      this.qrCodeArr = arrList;
      this.qrCodeDesc = { desc1: "", desc2: "" };
    },
    handlePreview(file: any) {
      console.log(file);
    },
    // @ts-ignore
    qrCodeChange(file, fileList) {
      const arr: any = [];
      fileList.map((item: any) => {
        arr.push(window.URL.createObjectURL(item.raw));
      });
      this.qrCodeArr = arr;
    },
    setAvatar() {
      if (this.$refs.avatarInput) {
        (this.$refs.avatarInput as HTMLElement).click();
      }
    },
  },
});
</script>

<style>
@font-face {
  font-family: "Open Sans";
  font-style: normal;
  font-weight: bold;
  src: url(~assets/OpenSans-Bold.ttf) format("truetype");
}
@font-face {
  font-family: "Open Sans";
  font-style: normal;
  font-weight: normal;
  src: url(~assets/OpenSans-Light.ttf) format("truetype");
}
@font-face {
  font-family: "SourceHanSerifSC";
  font-style: normal;
  font-weight: 300;
  src: url(~assets/SourceHanSerifSC-Heavy.otf) format("opentype");
}

@font-face {
  font-family: "Alibaba-PuHuiTi";
  font-style: normal;
  font-weight: normal;
  src: url(~assets/Alibaba-PuHuiTi-Light.ttf) format("opentype");
}

@font-face {
  font-family: "Alibaba-PuHuiTi";
  font-style: normal;
  font-weight: bold;
  src: url(~assets/Alibaba-PuHuiTi-Bold.ttf) format("opentype");
}

@font-face {
  font-family: "Alibaba-PuHuiTi";
  font-style: normal;
  font-weight: 300;
  src: url(~assets/Alibaba-PuHuiTi-Heavy.ttf) format("opentype");
}

h1 {
  margin-bottom: 15px;
  font-size: 20px;
  font-family: "SourceHanSerifSC", "Open Sans";
}

.poster-container {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  font-family: "Alibaba-PuHuiTi";
}

#poster-preview {
  position: relative;
  width: max-content;
  height: 600px;
  overflow: hidden;
  -webkit-user-select: none;
  user-select: none;
}

.bg {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  background: white;
  z-index: -20;
}

.poster-template {
  height: 600px;
}

.poster-content {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  text-align: center;
  color: #000;
  font-size: 2vh;
}

.author-img {
  position: absolute;
  z-index: -10;
}

.title {
  font-weight: bold;
  font-size: 3vh;
  font-family: "Alibaba-PuHuiTi";
  margin-top: 270px;
}

.name {
  font-weight: bold;
  font-size: 5vh;
  top: 55vh;
  position: absolute;
  left: 3.3vh;
  letter-spacing: 0.5vh;
}

.topic {
  margin-bottom: 0.5vh;
  font-size: 2.3vh;
  font-weight: bold;
}

.date {
  font-size: 2vh;
  position: absolute;
  top: 72vh;
  left: 3.4vh;
  font-weight: bold;
  color: #000;
  letter-spacing: 0.1vh;
}

.time {
  font-size: 2vh;
  position: absolute;
  top: 75vh;
  left: 3.4vh;
  font-weight: bold;
  color: #fff;
  letter-spacing: 0.1vh;
}

.location {
  font-size: 2vh;
  position: absolute;
  top: 85vh;
  left: 3.4vh;
  font-weight: bold;
  color: #fff;
  letter-spacing: 0.1vh;
  word-break: break-all;
  width: 21vh;
  text-align: left;
}
.qr-code-container {
  width: 23vh;
  height: 13vh;
  position: absolute;
  top: 77vh;
  left: 26vh;
  display: flex;
  justify-content: center;
}
.content {
  margin-right: 1vh;
  width: 10vh;
  height: 10vh;
  background-color: #fff;
  border-radius: 1vh;
}
.qr-code {
  padding: 0.5vh;
}
.qr-code img {
  width: 100%;
}
.desc {
  color: #fff;
  margin: 1vh 0 0 0;
  font-size: 1.8vh;
}

.el-form-item {
  margin-bottom: 5px;
}

.avatar-uploader {
  display: inline-block;
  margin-top: 10px;
  width: 40px;
  height: 40px;
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader:hover {
  border-color: #409eff;
}

.avatar-icon {
  font-size: 22px;
  color: #ccc;
  width: 40px;
  height: 40px;
  line-height: 40px;
  text-align: center;
}

.icon-panel {
  margin-top: 12px;
}

.el-upload:hover .avatar-icon,
a:hover .avatar-icon {
  color: #409eff;
}

.avatar {
  width: 40px;
  height: 40px;
  display: block;
}

.info,
.info a {
  color: #aaa;
}
</style>

function item(item: any, arg1: (any: any) => void) { throw new Error('Function
not implemented.'); }
