<style lang="less"></style>
<template>
  <div>
    <div class="h-panel w-800">
      <div class="h-panel-bar">
        <span class="h-panel-title">添加</span>
        <div class="h-panel-right">
          <Button color="primary" @click="create">添加</Button>
          <Button @click="$emit('close')" :text="true">取消</Button>
        </div>
      </div>
      <div class="h-panel-body">
        <Form ref="form" :validOnChange="true" :showErrorTip="true" :labelWidth="110" :rules="rules" :model="attach">
          <FormItem label="附件名" prop="name">
            <template v-slot:label>附件名</template>
            <input type="text" v-model="attach.name" />
          </FormItem>
          <FormItem label="附件">
            <template v-slot:label>附件</template>
            <input type="file" ref="file" />
            <warn :text="'仅支持' + extention + '格式文件'"></warn>
          </FormItem>
        </Form>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['cid'],
  data() {
    return {
      attach: {
        name: null,
        file: null
      },
      rules: {
        required: ['name']
      },
      extention: 'zip,pdf,jpeg,jpg,gif,png,md,doc,txt,csv'
    };
  },
  methods: {
    create() {
      if (this.$refs.file.files.length === 0) {
        HeyUI.$Message.warn('请上传附件');
        return;
      }
      let file = this.$refs.file.files[0];
      let extention = file.name.split('.').pop();
      if (this.extention.indexOf(extention) === -1) {
        HeyUI.$Message.warn('文件格式不支持');
        return;
      }
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let formData = new FormData();
        formData.append('file', file, file.name);
        formData.append('name', this.attach.name);
        formData.append('course_id', this.cid);
        this.$emit('success', formData);
      }
    },
    close() {
      this.$emit('close');
    }
  }
};
</script>
