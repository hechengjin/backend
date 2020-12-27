<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="goods">
        <Row :space="10">
          <Cell :width="24">
            <FormItem prop="goods_title" label="商品名">
              <input type="text" v-model="goods.goods_title" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="original_charge" label="商品原价">
              <input type="number" v-model="goods.goods_charge" min="0" />
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
      goods: {
        id: null,
        other_id: null,
        goods_title: null,
        goods_type: null,
        goods_charge: null
      },
      rules: {
        required: ['goods_id', 'goods_type', 'goods_title', 'goods_charge']
      },
      goodsList: [],
      types: []
    };
  },
  mounted() {
    this.getGoods();
  },
  methods: {
    getGoods() {
      R.Extentions.CodeExchanger.Goods.Edit({ id: this.id }).then(res => {
        this.goods = res.data;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.CodeExchanger.Goods.Update(this.goods).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
