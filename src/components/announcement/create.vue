<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="announcement">
        <FormItem label="标题" prop="title">
          <input type="text" v-model="announcement.title" />
        </FormItem>
        <FormItem label="内容" prop="announcement">
          <tinymce-editor v-model="announcement.announcement"></tinymce-editor>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import TinymceEditor from '../common/tinymce';

export default {
  components: { TinymceEditor },
  data() {
    return {
      announcement: {
        title: '',
        announcement: ''
      },
      rules: {
        required: ['title', 'announcement']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Announcement.Store(this.announcement).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
