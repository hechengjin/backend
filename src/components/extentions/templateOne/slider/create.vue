<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="slider">
        <FormItem label="排序" prop="sort">
          <template v-slot:label>排序</template>
          <input type="number" v-model="slider.sort" placeholder="小的数靠前" />
        </FormItem>
        <FormItem label="图片" prop="thumb">
          <template v-slot:label>图片</template>
          <image-upload v-model="slider.thumb" name="图片"></image-upload>
        </FormItem>
        <FormItem label="地址" prop="url">
          <template v-slot:label>地址</template>
          <input type="text" v-model="slider.url" />
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      slider: {
        sort: 0,
        thumb: null,
        url: null
      },
      rules: {
        required: ['url', 'sort', 'thumb']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.slider);
      }
    }
  }
};
</script>
