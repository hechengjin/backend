<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">备注</span>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form">
        <FormItem label="备注">
          <tinymce-editor v-model="user.remark"></tinymce-editor>
        </FormItem>
        <FormItem>
          <Button color="primary" @click="update">保存</Button>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import TinymceEditor from '../common/tinymce';

export default {
  components: {
    TinymceEditor
  },
  props: ['id'],
  data() {
    return {
      user: {
        id: this.id,
        remark: null
      }
    };
  },
  mounted() {
    R.Member.Remark(this.user).then(res => {
      this.user.remark = res.data.remark;
    });
  },
  methods: {
    update() {
      R.Member.RemarkUpdate(this.user).then(res => {
        this.$emit('success', this.user);
      });
    }
  }
};
</script>
