<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="practice">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="分类" prop="category_id">
              <Select v-model="practice.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
            </FormItem>
          </Cell>
          <Cell :width="18">
            <FormItem label="练习名" prop="name">
              <input type="text" v-model="practice.name" />
            </FormItem>
          </Cell>
          <Cell :width="24">
            <FormItem label="封面" prop="thumb">
              <image-upload v-model="practice.thumb" name="封面"></image-upload>
            </FormItem>
          </Cell>
          <Cell :width="24">
            <FormItem label="免费" prop="is_free">
              <h-switch v-model="practice.is_free" :trueValue="1" :falseValue="0"></h-switch>
              <warn text="所有人都可以参与考试。"></warn>
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10" v-if="practice.is_free === 0">
          <Cell :width="6">
            <FormItem label="会员免费" prop="is_vip_free">
              <h-switch v-model="practice.is_vip_free" :trueValue="1" :falseValue="0"></h-switch>
              <warn text="购买VIP会员的用户都可以参与考试。"></warn>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="价格" prop="charge">
              <input type="number" v-model="practice.charge" />
              <warn text="价格大于0的话用户可以购买此练习参与练习"></warn>
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      practice: {
        category_id: null,
        name: null,
        thumb: null,
        is_free: null,
        is_vip_free: null,
        charge: null
      },
      rules: {
        required: ['category_id', 'name', 'thumb']
      },
      createParams: {},
      categories: []
    };
  },
  mounted() {
    this.init();

    this.practice.is_free = 1;
    this.practice.is_vip_free = 0;
  },
  methods: {
    init() {
      R.Extentions.paper.Practice.Create().then(res => {
        this.categories = res.data.categories;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.paper.Practice.Store(this.practice).then(resp => {
          HeyUI.$Message.success('添加成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
