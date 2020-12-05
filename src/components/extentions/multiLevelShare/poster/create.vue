<template>
  <div style="padding: 15px;">
    <Form
      v-width="400"
      mode="block"
      ref="form"
      :validOnChange="true"
      :showErrorTip="true"
      :rules="rules"
      :model="poster"
    >
      <FormItem label="升序" prop="sort">
        <template v-slot:label>升序</template>
        <input type="number" v-model="poster.sort" />
      </FormItem>
      <FormItem label="海报名" prop="name">
        <template v-slot:label>海报名</template>
        <input type="text" v-model="poster.name" />
      </FormItem>
      <FormItem label="海报" prop="thumb">
        <template v-slot:label>海报</template>
        <image-upload v-model="poster.thumb" name="海报"></image-upload>
      </FormItem>
      <FormItem label="参数" prop="config">
        <template v-slot:label>参数</template>
        <textarea v-model="poster.config" rows="3"></textarea>
      </FormItem>
      <FormItem>
        <Button color="primary" @click="create">添加</Button>
      </FormItem>
    </Form>
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
