<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="update">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="book">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="分类" prop="cid">
              <Select v-model="book.cid" :datas="categories" keyName="id" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell :width="18">
            <FormItem label="书名" prop="name">
              <input type="text" v-model="book.name" />
            </FormItem>
          </Cell>
          <Cell :width="24">
            <FormItem label="封面" prop="thumb">
              <image-upload v-model="book.thumb" name="封面"></image-upload>
            </FormItem>
          </Cell>
        </Row>
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="价格" prop="charge">
              <div class="h-input-group">
                <input type="text" v-model="book.charge" />
                <span class="h-input-addon">元</span>
              </div>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="订阅人数" prop="user_count">
              <div class="h-input-group">
                <input type="text" v-model="book.user_count" />
                <span class="h-input-addon">人</span>
              </div>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="上架时间" prop="published_at">
              <DatePicker v-model="book.published_at" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
          <Cell :width="3">
            <FormItem label="显示" prop="is_show">
              <h-switch v-model="book.is_show"></h-switch>
            </FormItem>
          </Cell>
          <Cell :width="3">
            <FormItem label="会员免费" prop="is_vip_free">
              <h-switch v-model="book.is_vip_free"></h-switch>
            </FormItem>
          </Cell>
        </Row>

        <FormItem label="简短介绍" prop="short_desc">
          <template v-slot:label>简短介绍</template>
          <textarea v-model="book.short_desc"></textarea>
        </FormItem>

        <FormItem label="详情介绍" prop="original_desc">
          <template v-slot:label>详情介绍</template>
          <tinymce-editor v-model="book.original_desc"></tinymce-editor>
        </FormItem>

        <FormItem label="SEO描述" prop="seo_description">
          <template v-slot:label>SEO描述</template>
          <textarea v-model="book.seo_description"></textarea>
        </FormItem>
        <FormItem label="SEO关键字" prop="seo_keywords">
          <template v-slot:label>SEO关键字</template>
          <textarea v-model="book.seo_keywords"></textarea>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import TinymceEditor from '../../../common/tinymce';

export default {
  props: ['id'],
  components: {
    TinymceEditor
  },
  data() {
    return {
      book: {
        cid: '',
        name: '',
        is_show: 0,
        original_desc: '',
        thumb: '',
        charge: 0,
        short_desc: '',
        published_at: '',
        seo_description: '',
        seo_keywords: '',
        is_vip_free: 0,
        user_count: 0
      },
      rules: {
        required: ['cid', 'name', 'is_show', 'original_desc', 'thumb', 'charge', 'short_desc', 'published_at', 'is_vip_free']
      },
      categories: []
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Extentions.meeduBooks.Book.Create().then(res => {
        this.categories = res.data.categories;
      });
      R.Extentions.meeduBooks.Book.Edit({ id: this.id }).then(res => {
        this.book = res.data;
      });
    },
    update() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.book.render_desc = this.book.original_desc;
        this.$emit('success', this.book);
      }
    }
  }
};
</script>
