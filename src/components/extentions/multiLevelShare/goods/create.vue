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
              <Select v-model="goods.goods_type" :datas="types" :filterable="true" keyName="value" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="goods_id" label="商品">
              <Select v-model="goods.goods_id" :datas="goodsList" :filterable="true" keyName="id" titleName="title" @change="goodsChange"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="goods_title" label="商品名">
              <input type="text" v-model="goods.goods_title" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="original_charge" label="商品价格">
              <input type="number" v-model="goods.goods_charge" min="0" />
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="24">
            <FormItem prop="goods_thumb" label="商品封面">
              <image-upload v-model="goods.goods_thumb" name="商品封面"></image-upload>
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="8">
            <FormItem prop="reward" label="一级奖励">
              <input type="text" v-model="goods.reward" min="0" placeholder="单位：元" />
            </FormItem>
          </Cell>
          <Cell :width="8">
            <FormItem prop="reward" label="二级奖励">
              <input type="text" v-model="goods.reward2" min="0" placeholder="单位：元" />
            </FormItem>
          </Cell>
          <Cell :width="8">
            <FormItem prop="reward" label="三级奖励">
              <input type="text" v-model="goods.reward3" min="0" placeholder="单位：元" />
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
import TinymceEditor from '@/components/common/tinymce';

export default {
  components: { TinymceEditor },
  data() {
    return {
      goods: {
        goods_id: null,
        goods_title: null,
        goods_thumb: null,
        goods_charge: null,
        goods_type: null,
        reward: null,
        reward2: null,
        reward3: null
      },
      rules: {
        required: ['goods_id', 'goods_type', 'goods_charge', 'goods_title', 'reward', 'reward2', 'reward3']
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
      this.goods.goods_thumb = null;
      this.getGoodsList();
    }
  },
  mounted() {
    this.getGoodsList();
  },
  methods: {
    getGoodsList() {
      R.Extentions.multiLevelShare.Goods.Create({ goods_type: this.goods.goods_type }).then(res => {
        this.types = res.data.types;
        this.goodsList = res.data.data;
      });
    },
    goodsChange(goods) {
      this.goods.goods_charge = goods.charge;
      this.goods.goods_title = goods.title;
      this.goods.goods_thumb = goods.thumb;
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.multiLevelShare.Goods.Store(this.goods).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
