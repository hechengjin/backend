<style lang="less" scoped>
.alert-info {
  width: 100%;
  height: auto;
  float: left;
  margin-top: 5px;
  margin-bottom: 5px;
  font-size: 14px;
  color: #999999;
}
</style>

<template>
  <div>
    <div class="h-input-group">
      <input type="text" v-model="vid" placeholder="请点击右侧的上传按钮或者直接输入视频文件fileId" />
      <Button color="primary" @click="selectVideo">
        <i class="h-icon-upload"></i> 上传视频<span v-show="process">，进度：{{ process }}</span>
      </Button>
    </div>
    <div class="alert-info">请上传MP4格式视频</div>
    <input type="file" ref="aliyunfile" v-show="false" @change="fileChange" />
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: ''
    }
  },
  mounted() {
    this.aliyun = new window.AliyunUpload.Vod({
      partSize: 1048576,
      parallel: 5,
      retryCount: 3,
      retryDuration: 2,
      onUploadstarted: uploadInfo => {
        // console.log("onUploadStarted:" + uploadInfo.file.name + ", endpoint:" + uploadInfo.endpoint + ", bucket:" + uploadInfo.bucket + ", object:" + uploadInfo.object);
        if (uploadInfo.videoId) {
          R.VideoUpload.AliyunAuthTokenRefresh({
            video_id: uploadInfo.videoId
          }).then(res => {
            if (res.status !== 0) {
              HeyUI.$Message.error(res.message);
            } else {
              this.aliyun.setUploadAuthAndAddress(uploadInfo, res.data.upload_auth, res.data.upload_address, res.data.video_id);
            }
          });
        } else {
          R.VideoUpload.AliyunAuthTokenCreate({
            title: uploadInfo.file.name,
            filename: uploadInfo.file.name
          }).then(res => {
            if (res.status !== 0) {
              HeyUI.$Message.error(res.message);
            } else {
              this.aliyun.setUploadAuthAndAddress(uploadInfo, res.data.upload_auth, res.data.upload_address, res.data.video_id);
            }
          });
        }
      },
      onUploadSucceed: uploadInfo => {
        console.log(uploadInfo);
        this.vid = uploadInfo.videoId;
        this.process = '上传成功';
        this.status = false;
        // console.log("onUploadSucceed: " + uploadInfo.file.name + ", endpoint:" + uploadInfo.endpoint + ", bucket:" + uploadInfo.bucket + ", object:" + uploadInfo.object);
      },
      onUploadFailed: (uploadInfo, code, message) => {
        // console.log("onUploadFailed: file:" + uploadInfo.file.name + ",code:" + code + ", message:" + message);
        this.process = '上传失败:' + message + ':code:' + code;
      },
      onUploadProgress: (uploadInfo, totalSize, loadedPercent) => {
        this.process = Math.ceil(loadedPercent * 100) + '%';
        // console.log("onUploadProgress:file:" + uploadInfo.file.name + ", fileSize:" + totalSize + ", percent:" + Math.ceil(loadedPercent * 100) + "%");
      },
      onUploadTokenExpired: uploadInfo => {
        R.VideoUpload.AliyunAuthTokenRefresh({
          video_id: uploadInfo.videoId
        }).then(res => {
          if (res.data.code === 500) {
            HeyUI.$Message.error(res.data.message);
          } else {
            this.aliyun.resumeUploadWithAuth(res.data.upload_auth);
          }
        });
        // uploader.resumeUploadWithAuth(uploadAuth);
      },
      onUploadEnd: uploadInfo => {}
    });
  },
  data() {
    return {
      vid: this.value,
      process: '',
      aliyun: null,
      status: false
    };
  },
  methods: {
    selectVideo() {
      if (this.status) {
        return;
      }
      this.$refs.aliyunfile.click();
    },
    fileChange(event) {
      if (event.target.files.length === 0) {
        return;
      }
      this.status = true;
      this.aliyun.addFile(event.target.files[0], null, null, null, '');
      this.aliyun.startUpload();
    }
  },
  watch: {
    value(newVal, oldVal) {
      this.vid = newVal;
    },
    vid(newVal, oldVal) {
      this.$emit('input', newVal);
    }
  }
};
</script>
