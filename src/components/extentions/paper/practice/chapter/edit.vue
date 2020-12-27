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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="chapter">
        <Row :space="10">
          <Cell :width="12">
            <FormItem label="章节名" prop="name">
              <input type="text" v-model="chapter.name" />
            </FormItem>
          </Cell>
          <Cell :width="12">
            <FormItem label="升序" prop="sort">
              <input type="number" v-model="chapter.sort" />
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['pid', 'id'],
  data() {
    return {
      chapter: {
        name: '',
        sort: 0,
        pid: this.pid
      },
      rules: {
        required: ['name', 'sort']
      }
    };
  },
  mounted() {
    R.Extentions.paper.PracticeChapter.Edit({ id: this.id }).then(res => {
      this.chapter = res.data.data;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.paper.PracticeChapter.Update(this.chapter).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
