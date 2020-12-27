<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="page">
        <FormItem label="唯一标识" prop="sign">
          <template v-slot:label>唯一标识</template>
          <input type="text" v-model="page.sign" />
        </FormItem>
        <FormItem label="标题" prop="title">
          <template v-slot:label>标题</template>
          <input type="text" v-model="page.title" />
        </FormItem>
        <FormItem label="Seo关键字" prop="title">
          <template v-slot:label>Seo关键字</template>
          <input type="text" v-model="page.seo_keywords" />
        </FormItem>
        <FormItem label="Seo描述" prop="title">
          <template v-slot:label>Seo描述</template>
          <input type="text" v-model="page.seo_description" />
        </FormItem>
        <FormItem label="内容" prop="content">
          <template v-slot:label>内容</template>
          <tinymce-editor v-model="page.content"></tinymce-editor>
        </FormItem>
        <FormItem label="继承布局" prop="is_show">
          <template v-slot:label>继承布局</template>
          <h-switch v-model="page.is_inherit"></h-switch>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import TinymceEditor from '@/components/common/tinymce';

export default {
  props: ['id'],
  components: {
    TinymceEditor
  },
  data() {
    return {
      page: {
        sign: '',
        title: '',
        seo_keywords: '',
        seo_description: '',
        content: '',
        is_inherit: 1
      },
      rules: {
        required: ['sign', 'title', 'content']
      }
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Extentions.singlePage.Page.Edit({ id: this.id }).then(res => {
        this.page = res.data;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.page);
      }
    }
  }
};
</script>
