<template>
  <tinymce :id="id" v-model="data" :other_options="config"></tinymce>
</template>

<script>
import tinymce from 'vue-tinymce-editor';

export default {
  props: {
    value: {
      type: String,
      default: ''
    }
  },
  components: {
    tinymce: tinymce
  },
  data() {
    return {
      data: this.value,
      id: 'tinymce' + Math.ceil(Math.random() * 100),
      config: {
        height: '144px',
        language: 'zh_CN',
        convert_urls: false,
        language_url: 'https://cdn.jsdelivr.net/npm/tinymce-i18n@19.2.11/langs/zh_CN.js',
        automatic_uploads: true,
        images_upload_handler: (blobInfo, success, failure) => {
          // 图片异步上传处理函数
          var xhr, formData;

          xhr = new XMLHttpRequest();
          xhr.withCredentials = false;
          xhr.open('POST', '/backend/api/v1/upload/image/tinymce');
          xhr.setRequestHeader('Authorization', 'Bearer ' + Utils.getLocal('token'));

          xhr.onload = function () {
            var json;

            if (xhr.status !== 200) {
              failure('HTTP Error: ' + xhr.status);
              return;
            }

            json = JSON.parse(xhr.responseText);
            if (!json || typeof json.location !== 'string') {
              failure('Invalid JSON: ' + xhr.responseText);
              return;
            }
            success(json.location);
          };

          formData = new FormData();
          formData.append('file', blobInfo.blob(), blobInfo.filename());

          xhr.send(formData);
        }
      }
    };
  },
  watch: {
    value(newVal, oldVal) {
      this.data = newVal;
    },
    data(newVal, oldVal) {
      this.$emit('input', newVal);
    }
  }
};
</script>
