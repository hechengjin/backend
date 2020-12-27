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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="topic">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="分类" prop="cid">
              <Select v-model="topic.cid" :datas="categories" keyName="id" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell :width="18">
            <FormItem label="标题" prop="title">
              <input type="text" v-model="topic.title" />
            </FormItem>
          </Cell>
          <Cell :width="24">
            <FormItem label="封面" prop="thumb">
              <image-upload v-model="topic.thumb" name="封面"></image-upload>
            </FormItem>
          </Cell>
          <Cell :width="3">
            <FormItem label="显示" prop="is_show">
              <h-switch v-model="topic.is_show"></h-switch>
            </FormItem>
          </Cell>
          <Cell :width="3">
            <FormItem label="登录查看" prop="is_need_login">
              <template v-slot:label>登录查看</template>
              <h-switch v-model="topic.is_need_login"></h-switch>
            </FormItem>
          </Cell>
          <Cell :width="3">
            <FormItem label="价格" prop="charge">
              <input type="number" min="0" max="2000" v-model="topic.charge" />
            </FormItem>
          </Cell>
          <Cell :width="3">
            <FormItem label="会员免费" prop="is_vip_free" v-if="topic.charge > 0">
              <h-switch v-model="topic.is_vip_free"></h-switch>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="排序时间" prop="sorted_at">
              <DatePicker v-model="topic.sorted_at" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
        </Row>
        <FormItem label="免费内容" prop="free_content">
          <markdown v-model="topic.free_content" uid="_topic_free_content"></markdown>
          <warn text="该内容所有用户都可以看到，不管是文章收费还是需要登录查看。"></warn>
        </FormItem>
        <FormItem label="文章内容" prop="original_content">
          <markdown v-model="topic.original_content" uid="_topic_original_content"></markdown>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import markdown from '@/components/common/markdown';

export default {
  props: ['id'],
  components: {
    markdown
  },
  data() {
    return {
      topic: {
        cid: '',
        title: '',
        is_show: 0,
        is_need_login: 0,
        is_vip_free: 0,
        free_content: '',
        free_content_render: '',
        charge: 0,
        original_content: '',
        sorted_at: null
      },
      rules: {
        required: ['cid', 'title', 'is_show', 'original_content']
      },
      categories: []
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Extentions.meeduTopics.Topic.Edit({ id: this.id }).then(res => {
        this.topic = res.data;
      });
      R.Extentions.meeduTopics.Category.List().then(res => {
        this.categories = res.data;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.topic.render_content = localStorage.getItem('markdown_content_val_topic_original_content');
        this.topic.free_content_render = localStorage.getItem('markdown_content_val_topic_free_content');
        this.$emit('success', this.topic);
      }
    }
  }
};
</script>
