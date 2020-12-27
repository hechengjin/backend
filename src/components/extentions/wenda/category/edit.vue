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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="category">
        <Row :space="10">
          <Cell :width="12">
            <FormItem label="分类名" prop="name">
              <input type="text" v-model="category.name" />
            </FormItem>
          </Cell>

          <Cell :width="12">
            <FormItem label="排序" prop="sort">
              <input type="number" v-model="category.sort" />
            </FormItem>
          </Cell>
        </Row>
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
    R.Extentions.wenda.Category.Edit({ id: this.id }).then(res => {
      this.category = res.data.data;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.category);
      }
    }
  }
};
</script>
