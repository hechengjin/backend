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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="category">
        <FormItem label="父级" prop="parent_id">
          <template v-slot:label>父级</template>
          <Select v-model="category.parent_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
        </FormItem>
        <FormItem label="分类名" prop="name">
          <template v-slot:label>分类名</template>
          <input type="text" v-model="category.name" />
        </FormItem>
        <FormItem label="排序" prop="sort">
          <template v-slot:label>排序</template>
          <input type="number" v-model="category.sort" />
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      category: {
        name: '',
        sort: 0,
        parent_id: 0
      },
      categories: [],
      rules: {
        required: ['name', 'sort']
      }
    };
  },
  mounted() {
    R.Extentions.xiaoBanKe.CourseCategory.Create().then(res => {
      this.categories = res.data.categories;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.xiaoBanKe.CourseCategory.Store(this.category).then(res => {
          this.$emit('success');
        });
      }
    }
  }
};
</script>
