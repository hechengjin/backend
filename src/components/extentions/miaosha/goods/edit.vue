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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="goods">
        <Row :space="10">
          <Cell :width="6">
            <FormItem prop="goods_title" label="商品名">
              <input type="text" v-model="goods.goods_title" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="goods_charge" label="商品原价">
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
          <Cell :width="24">
            <FormItem prop="desc" label="详细介绍">
              <tinymce-editor v-model="goods.desc"></tinymce-editor>
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="6">
            <FormItem prop="charge" label="秒杀价格">
              <input type="number" v-model="goods.charge" min="0" />
              <warn text="单位：元，只能设置为整数。"></warn>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="num" label="秒杀数量">
              <input type="number" v-model="goods.num" min="0" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="started_at" label="开始时间">
              <DatePicker v-model="goods.started_at" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="end_at" label="结束时间">
              <DatePicker v-model="goods.end_at" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="6">
            <FormItem prop="page_title" label="秒杀页面标题">
              <input type="text" v-model="goods.page_title" />
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
        goods_id: null,
        goods_type: null,
        goods_title: null,
        goods_thumb: null,
        goods_charge: null,
        charge: null,
        num: null,
        started_at: null,
        end_at: null,
        page_title: null,
        desc: null
      },
      rules: {
        required: [
          'goods_id',
          'goods_type',
          'goods_title',
          'goods_thumb',
          'goods_charge',
          'desc',
          'num',
          'charge',
          'started_at',
          'end_at',
          'page_title'
        ]
      },
      goodsList: [],
      types: []
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Extentions.miaoSha.Goods.Edit({ id: this.id }).then(res => {
        this.goods = res.data;
        this.goods.goods_charge = this.goods.original_charge;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let data = this.goods;
        this.$emit('success', data);
      }
    }
  }
};
</script>
