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
  props: ['id'],
  components: { TinymceEditor },
  data() {
    return {
      goods: {
        goods_title: null,
        goods_thumb: null,
        goods_charge: null,
        reward: null,
        reward2: null,
        reward3: null
      },
      rules: {
        required: ['goods_charge', 'goods_title', 'reward', 'reward2', 'reward3']
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
      R.Extentions.multiLevelShare.Goods.Edit({ id: this.id }).then(res => {
        this.goods = res.data;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.multiLevelShare.Goods.Update(this.goods).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
