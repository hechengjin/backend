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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="article">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="章节" prop="book_cid">
              <Select v-model="article.book_cid" :datas="cs" keyName="id" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell :width="18">
            <FormItem label="标题" prop="title">
              <input type="text" v-model="article.title" placeholder="请输入标题" />
            </FormItem>
          </Cell>
        </Row>
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="价格" prop="charge">
              <div class="h-input-group" v-width="100">
                <input type="text" v-model="article.charge" />
                <span class="h-input-addon">元</span>
              </div>
              <warn text="价格为0即视为试看，可免费阅读"></warn>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="显示" prop="is_show">
              <h-switch v-model="article.is_show"></h-switch>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="上架时间" prop="published_at">
              <DatePicker v-model="article.published_at" v-width="200" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
        </Row>

        <FormItem label="内容" prop="original_content">
          <mk-editor v-model="article.original_content"></mk-editor>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import MkEditor from '@/components/common/markdown.vue';

export default {
  props: ['bid'],
  components: {
    MkEditor
  },
  data() {
    return {
      article: {
        bid: this.bid,
        book_cid: 0,
        title: '',
        original_content: '',
        render_content: '',
        published_at: '',
        charge: 0
      },
      rules: {
        required: ['bid', 'book_cid', 'is_show', 'title', 'original_content', 'published_at']
      },
      books: [],
      chapters: []
    };
  },
  mounted() {
    this.init();
  },
  computed: {
    cs() {
      if (!this.article.bid) {
        return [];
      }
      return this.chapters[this.article.bid];
    }
  },
  methods: {
    init() {
      R.Extentions.meeduBooks.Article.Create().then(res => {
        this.books = res.data.books;
        this.chapters = res.data.chapters;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.article.render_content = localStorage.getItem('markdown_content_val');
        this.$emit('success', this.article);
      }
    }
  }
};
</script>
