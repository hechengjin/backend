<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="banner">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="Banner名" prop="name">
              <input type="text" v-model="banner.name" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="升序" prop="sort">
              <input type="number" v-model="banner.sort" />
            </FormItem>
          </Cell>
          <Cell :width="12">
            <FormItem label="课程" prop="course_ids">
              <Select v-model="banner.course_ids" :datas="courses" keyName="id" titleName="title" :multiple="true"></Select>
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
      banner: {
        name: '',
        sort: 0,
        course_ids: []
      },
      rules: {
        required: ['name', 'course_ids', 'sort']
      },
      courses: []
    };
  },
  mounted() {
    R.IndexBanner.Create().then(res => {
      this.courses = res.data.courses;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.IndexBanner.Store(this.banner).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
