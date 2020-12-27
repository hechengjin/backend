<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="chapter">
        <FormItem label="章节名" prop="name">
          <input type="text" v-model="chapter.name" />
        </FormItem>
        <FormItem label="升序" prop="sort">
          <input type="number" v-model="chapter.sort" />
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id', 'bid'],
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
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Extentions.meeduBooks.Chapter.Edit({ id: this.id }).then(res => {
        this.chapter = res.data;
      });
    },
    create() {
      this.$emit('success', this.chapter);
    }
  }
};
</script>
