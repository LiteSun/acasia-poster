<template>
  <el-row type="flex" class="poster-container" justify="center">
    <el-col :span="12">
      <div id="poster-preview"
        :style="{'cursor': imageUrl ? 'move' : 'default'}"
      >
        <div class="bg"></div>
        <img class="poster-template" src="meetup.jpg">
        <div class="poster-content">
          <div class="title">{{ title }}</div>
          <div class="name" :style="{'font-size': '3.7vh'}">{{ name }}</div>
          <div class="date" :style="{'font-size': '2.3vh'}">{{ date }}</div>
          <div class="time">{{ time }}</div>
          <div class="location">{{ location }}</div>
          <div class="qr-code-container" :style="{'left': qrCodeArr.length === 2 ? '27vh' : '28vh'}">
          <div class="content" v-for="(item,index) in qrCodeArr" :key="item">
            <div class="qr-code">
            <img :src="item" ref="avatar" />
            </div>
            <p class="desc">{{qrCodeDesc[`desc${++index}`]}}</p>
          </div>
          </div>
        </div>
      </div>
    </el-col>
    <el-col :span="8" class="poster-control" v-loading="isDownloading" element-loading-text="生成海报中">
      <el-row>
        <h1>Apache APISIX Meetup 邀请函生成器</h1>
      </el-row>
      <el-form>
        <el-form-item label="主题">
            <el-input v-model="title" />
        </el-form-item>
        <el-form-item label="特邀嘉宾">
            <el-input v-model="name" />
        </el-form-item>
          <el-form-item label="日期">
            <el-input v-model="date" />
        </el-form-item>
          </el-form-item>
          <el-form-item label="时间">
            <el-input v-model="time" />
        </el-form-item>
        <el-form-item label="地点">
          <el-input v-model="location" />
        </el-form-item>
            <el-form-item label="二维码照片">
            <el-upload
              class="upload-demo"
              :auto-upload="false"
              :limit="2"
              :on-change="qrCodeChange"
              action="https://jsonplaceholder.typicode.com/posts/"
              :on-preview="handlePreview"
              :on-remove="handleRemove"
              list-type="picture">
              <el-button size="small" type="primary">添加二维码</el-button>
            </el-upload>
        </el-form-item>
          <el-form-item label="二维码-1-描述" v-if="qrCodeArr.length >= 1">
          <el-input v-model="qrCodeDesc.desc1" />
        </el-form-item>
          <el-form-item label="二维码-2-描述" v-if="qrCodeArr.length === 2">
          <el-input v-model="qrCodeDesc.desc2" />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="download()"
          >
            生成海报
          </el-button>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<script lang="ts">
import Vue from 'vue';
// @ts-ignore
import domtoimage from 'retina-dom-to-image';
import trackKeyRaw from './data/track-keynote';

type SpeechInfo = {
  track: string,
  title: string,
  name: string,
  topic: string,
  time: string,
  isKeynote: boolean
};

const trackKey = trackKeyRaw.split('\n').slice(2).filter(line => !!line);

function getTrackInfo(raw: string, isZh: boolean): SpeechInfo[] {
  let infos = raw.split('\n').slice(2)
    .filter(line => !!line)
    .map(line => {
      const arr = line.split(',');
      return {
        track: arr[2],
        title: arr[4],
        name: arr[1],
        topic: arr[3],
        time: arr[5],
        isKeynote: false
      };
    });

  // add keynote
  infos = infos.concat(
    trackKey.filter(line => {
      return isZh === /[\u4e00-\u9fa5]/.test(line);
    })
      .map(line => {
        const arr = line.split(',');
        return {
          track: arr[0],
          title: arr[2],
          name: arr[1],
          topic: arr[3],
          time: arr[4],
          isKeynote: true
        };
      })
  );

  return infos;
}

export default Vue.extend({
  data() {
    return {
      name: '',
      title: '',
      date: '',
      time: '',
      location: '',
      imageUrl: null as unknown as string,
      topic: '',
      isKeynote: false,
      avatarInput: null,
      isDownloading: false,
      posterBase64: '',
      qrCodeArr: [],
      qrCodeDesc: {desc1:'',desc2:''},
    };
  },

  mounted() {
  },

  methods: {
    download() {
      this.isDownloading = true;
      domtoimage.toJpeg(document.getElementById('poster-preview'))
        .then((url: string) => {
          this.posterBase64 = url;
          this.isDownloading = false;
          const link = document.createElement('a');
          link.href = url;
          link.download = this.name + '.jpeg';
          link.click();
        })
    },
      handleRemove(file:any, fileList:any) {
        let arrList:any = []
        fileList.map((file: any) => {
          arrList.push(window.URL.createObjectURL(file.raw))
        })
        this.qrCodeArr = arrList;
        this.qrCodeDesc = {desc1:'',desc2:''}
      },
      handlePreview(file:any) {
        console.log(file);
      },
      // @ts-ignore
      qrCodeChange(file, fileList) {
        const arr:any = [];
        fileList.map((item: any) => {
          arr.push(window.URL.createObjectURL(item.raw));
        })
        this.qrCodeArr = arr;
      },
    setAvatar() {
      if (this.$refs.avatarInput) {
        (this.$refs.avatarInput as HTMLElement).click();
      }
    },
  }
})
</script>

<style>
@font-face {
  font-family: 'Open Sans';
  font-style: normal;
  font-weight: bold;
  src: url(~assets/OpenSans-Bold.ttf) format('truetype');
}
@font-face {
  font-family: 'Open Sans';
  font-style: normal;
  font-weight: normal;
  src: url(~assets/OpenSans-Light.ttf) format('truetype');
}
@font-face {
  font-family: 'SourceHanSerifSC';
  font-style: normal;
  font-weight: 300;
  src: url(~assets/SourceHanSerifSC-Heavy.otf) format('opentype');
}

h1 {
  margin-bottom: 15px;
  font-size: 20px;
  font-family: 'SourceHanSerifSC', 'Open Sans';
}

.poster-container {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  font-family: 'Open Sans';
}

  #poster-preview {
    position: relative;
    width: max-content;
    height: 100vh;
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
      height: 100vh;
    }

    .poster-content {
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      text-align: center;
      color: #fff;
      font-size: 2vh;
    }

      .author-img {
        position: absolute;
        z-index: -10;
      }

      .title {
        font-weight: bold;
        position: absolute;
        top: 28vh;
        left: 17vh;
        font-size: 3vh;
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
        color: #fff;
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
        justify-content: center
      }
      .content{
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
  border-color: #409EFF;
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

.el-upload:hover .avatar-icon, a:hover .avatar-icon {
  color: #409EFF;
}

.avatar {
  width: 40px;
  height: 40px;
  display: block;
}

.info, .info a {
  color: #aaa;
}
</style>

function item(item: any, arg1: (any: any) => void) {
  throw new Error('Function not implemented.');
}
