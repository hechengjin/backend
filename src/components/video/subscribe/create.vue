<style lang="less"></style>
<template>
  <div class="table-basic-vue frame-page h-panel h-panel-margin-0 w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
    </div>
    <div class="h-panel-body">
      <Form
        ref="form"
        mode="block"
        :validOnChange="true"
        :showErrorTip="true"
        :labelWidth="110"
        :rules="rules"
        :model="data"
      >
        <FormItem label="UID" prop="user_id">
          <input type="text" v-model="data.user_id" placeholder="用户ID" />
        </FormItem>

        <FormItem>
          <Button color="primary" @click="create">添加</Button>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      data: {
        video_id: this.id,
        user_id: null
      },
      rules: {
        required: ['user_id']
      }
    };
  },
  watch: {
    id(newVal) {
      this.data.video_id = newVal;
    }
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Video.SubscribeCreate(this.data).then(() => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>