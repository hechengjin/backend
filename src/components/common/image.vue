<style lang="less">
.image-upload-box {
  width: 100%;
  height: auto;
  float: left;
  display: flex;

  .input-box {
    flex: 1;
    input {
      width: 100%;
      height: @input-height;
      float: left;
      border-top-right-radius: 0;
      border-bottom-right-radius: 0;
    }
  }

  .buttons {
    width: auto;
    height: auto;

    button {
      display: block;
      width: auto;
      height: @input-height;
      float: left;
      background-color: @primary-color;
      color: white;
      border: 0;
      border-radius: 0;
      padding-left: 10px;
      padding-right: 10px;
      cursor: pointer;

      &.last {
        border-top-right-radius: 4px;
        border-bottom-right-radius: 4px;
      }
    }
  }
}
.image-upload-text {
  width: 100%;
  height: auto;
  float: left;
  margin-top: 5px;
  font-size: 14px;
  color: #999999;
  padding-left: 5px;
}
.image-uploaded-box {
  width: 100%;
  height: auto;
  float: left;
  margin-top: 5px;
  padding-left: 5px;
  background-color: rgba(0, 0, 0, 0.02);
}
</style>
<template>
  <div>
    <div class="image-upload-box">
      <div class="input-box">
        <input type="text" placeholder="您也可以手动输入图片URL地址" v-model="image" />
      </div>
      <div class="buttons">
        <button @click="selectImage" class="last">
          <i class="h-icon-upload"></i>
          本地上传
        </button>
      </div>
    </div>
    <div class="image-upload-text" v-if="help">{{ help }}</div>
    <div class="image-uploaded-box">
      <img :src="image" width="150px" v-if="image" />
      <input type="file" v-show="false" @change="change" ref="file" />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: ''
    },
    name: {
      type: String,
      default: '选择图片'
    },
    help: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      image: this.value
    };
  },
  methods: {
    selectImage() {
      this.$refs.file.click();
    },
    change(event) {
      let file = event.target.files[0];
      this.upload(file);
    },
    upload(file) {
      let xhr = new XMLHttpRequest();
      xhr.open('post', '/backend/api/v1/upload/image/tinymce', true);
      xhr.setRequestHeader('Authorization', 'Bearer ' + Utils.getLocal('token'));
      let formData = new FormData();
      formData.set('file', file);
      xhr.send(formData);
      xhr.onload = () => {
        var json;
        if (xhr.status !== 200) {
          return;
        }
        json = JSON.parse(xhr.responseText);
        if (!json || typeof json.location !== 'string') {
          return;
        }
        this.image = json.location;
      };
    },
    downloadImage() {
      if (this.image.length === 0) {
        Hey.$Message.warn('请输入图片url地址');
        return;
      }
      R.Upload.ImageDownload({ url: this.image }).then(res => {
        this.image = res.data.url;
      });
    }
  },
  watch: {
    value(newVal, oldVal) {
      this.image = newVal;
    },
    image(newVal, oldVal) {
      this.$emit('input', newVal);
    }
  }
};
</script>
