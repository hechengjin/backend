<template>
  <div class="h-panel w-1000">
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
            <FormItem prop="other_id" label="商品">
              <Select v-model="goods.other_id" :datas="goodsList" :filterable="true" keyName="id" titleName="title" @change="goodsChange"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="goods_title" label="商品名">
              <input type="text" v-model="goods.goods_title" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="original_charge" label="商品原价">
              <input type="number" v-model="goods.original_charge" min="0" />
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
          <Cell :width="4">
            <FormItem prop="charge" label="价格">
              <input type="text" v-model="goods.charge" min="0" placeholder="单位：元" />
            </FormItem>
          </Cell>
          <Cell :width="4">
            <FormItem prop="charge" label="人数">
              <input type="number" v-model="goods.people_num" min="0" placeholder="组团上限" />
            </FormItem>
          </Cell>
          <Cell :width="4">
            <FormItem prop="charge" label="有效期">
              <input type="number" v-model="goods.time_limit" min="0" placeholder="单位：天" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="charge" label="开始时间">
              <DatePicker v-model="goods.started_at" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem prop="charge" label="结束时间">
              <DatePicker v-model="goods.ended_at" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="24">
            <FormItem prop="page_title" label="页面标题">
              <input type="text" v-model="goods.page_title" placeholder="pc或者手机打开团购页面显示的标题" />
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
        other_id: null,
        goods_title: null,
        goods_thumb: null,
        goods_type: null,
        charge: null,
        original_charge: null,
        people_num: null,
        started_at: null,
        ended_at: null,
        page_title: null,
        desc: null
      },
      rules: {
        required: [
          'other_id',
          'goods_type',
          'goods_title',
          'goods_thumb',
          'people_num',
          'charge',
          'original_charge',
          'started_at',
          'end_at',
          'page_title',
          'desc'
        ]
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
    this.init();
  },
  methods: {
    init() {
      this.getGoodsList();
    },
    getGoodsList() {
      R.Extentions.tuanGou.Goods.Create({ type: this.goods.goods_type }).then(res => {
        this.types = res.data.types;
        this.goodsList = res.data.data;
      });
    },
    goodsChange(goods) {
      this.goods.original_charge = goods.charge;
      this.goods.goods_title = goods.title;
      this.goods.goods_thumb = goods.thumb;
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.tuanGou.Goods.Store(this.goods).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
