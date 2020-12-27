<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="category">
        <FormItem label="分类名" prop="name">
          <input type="text" v-model="category.name" />
        </FormItem>
        <FormItem label="升序" prop="sort">
          <input type="number" v-model="category.sort" />
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
      category: {
        name: '',
        sort: 0
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
      R.Extentions.meeduBooks.Category.Edit({ id: this.id }).then(res => {
        this.category = res.data;
      });
    },
    create() {
      this.$emit('success', this.category);
    }
  }
};
</script>
