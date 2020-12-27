<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">导入试题</span>
      <div class="h-panel-right">
        <Button @click="submitImport" color="primary">导入</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <a :href="url + '?token=' + token" target="_blank">导入模板下载</a>
      </div>
      <div class="float-box mb-10">
        <p>类型选项：单选题，多选题，判断题，填空题，问答题</p>
        <p>难度选项：简单，中等，难，特别难</p>
        <p>单选答案：请填写对应答案的序号，如果：1</p>
        <p>多选答案：正确答案的序号用英文逗号连接，如：1,2,3</p>
        <p>判断题答案：正确填写1，错误填写0</p>
      </div>
      <div class="float-box mb-10">
        <input type="file" ref="file" />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['url'],
  data() {
    return {
      file: null,
      token: ''
    };
  },
  mounted() {
    this.token = Utils.getLocal('token');
    this.init();
  },
  methods: {
    init() {},
    submitImport() {
      if (this.$refs.file.files.length === 0) {
        this.$Message.error('请选择文件');
        return;
      }
      let file = this.$refs.file.files[0];
      var form = new FormData();
      form.append('file', file);
      var xhr = new XMLHttpRequest();
      var action = '/backend/addons/Paper/question/import/csv?token=' + Utils.getLocal('token');
      xhr.open('POST', action);
      xhr.send(form);
      xhr.onreadystatechange = () => {
        if (xhr.readyState === 4 && xhr.status === 200) {
          var resultObj = JSON.parse(xhr.responseText);
          if (resultObj.status === 0) {
            this.$Message.success('成功');
            this.$emit('success');
          } else {
            this.$Message.error(resultObj.message);
          }
        }
      };
    }
  }
};
</script>
