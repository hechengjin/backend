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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="goods">
        <Row :space="10">
          <Cell :width="6">
            <FormItem prop="goods_type" label="类型">
              <Select v-model="goods.goods_type" :datas="types" :filterable="true" keyName="key" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell :width="18">
            <FormItem prop="goods_id" label="商品">
              <Select
                v-model="goods.goods_id"
                :datas="goodsList"
                :filterable="true"
                keyName="goods_id"
                titleName="goods_title"
                @change="goodsChange"
              ></Select>
            </FormItem>
          </Cell>
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
  data() {
    return {
      goods: {
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
  watch: {
    'goods.goods_type'() {
      this.goods.goods_id = null;
      this.goods.goods_title = null;
      this.goods.goods_charge = null;
      this.getGoodsList();
    }
  },
  mounted() {
    this.getGoodsList();
  },
  methods: {
    getGoodsList() {
      R.Extentions.CodeExchanger.Goods.Create({ goods_type: this.goods.goods_type }).then(res => {
        this.types = res.data.types;
        this.goodsList = res.data.data;
      });
    },
    goodsChange(goods) {
      this.goods.goods_charge = goods.goods_charge;
      this.goods.goods_title = goods.goods_title;
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.CodeExchanger.Goods.Store(this.goods).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
