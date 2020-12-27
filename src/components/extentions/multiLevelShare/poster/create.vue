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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="poster">
        <FormItem label="升序" prop="sort">
          <input type="number" v-model="poster.sort" />
        </FormItem>
        <FormItem label="海报名" prop="name">
          <input type="text" v-model="poster.name" />
        </FormItem>
        <FormItem label="海报" prop="thumb">
          <image-upload v-model="poster.thumb" name="海报"></image-upload>
        </FormItem>
        <FormItem label="参数" prop="config">
          <textarea v-model="poster.config" rows="3"></textarea>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      poster: {
        name: '',
        sort: 0,
        thumb: null,
        config: null
      },
      rules: {
        required: ['name', 'sort', 'thumb', 'config']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.poster);
      }
    }
  }
};
</script>
