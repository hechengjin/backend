<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="chapter">
        <FormItem label="章节名" prop="name">
          <input type="text" v-model="chapter.name" />
        </FormItem>
        <FormItem label="升序" prop="sort">
          <input type="number" v-model="chapter.sort" />
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
  props: ['bid'],
  data() {
    return {
      chapter: {
        name: '',
        sort: 0,
        bid: this.bid
      },
      rules: {
        required: ['name', 'sort']
      }
    };
  },
  methods: {
    create() {
      this.$emit('success', this.chapter);
    }
  }
};
</script>
