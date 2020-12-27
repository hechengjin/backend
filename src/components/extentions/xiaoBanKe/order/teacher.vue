<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">配置</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form v-width="400" mode="block" ref="form">
        <FormItem>
          <template v-slot:label>老师</template>
          <Select v-model="teacher_id" :datas="teachers" keyName="id" titleName="name" :filterable="true"></Select>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id', 'course_id'],
  data() {
    return {
      teachers: [],
      teacher_id: null
    };
  },
  mounted() {
    R.Extentions.xiaoBanKe.Order.SetTeacherParams({ id: this.course_id }).then(res => {
      this.teachers = res.data.teachers;
    });
  },
  methods: {
    create() {
      R.Extentions.xiaoBanKe.Order.SetTeacher({ id: this.id, teacher_id: this.teacher_id }).then(res => {
        this.$emit('success');
        å;
      });
    }
  }
};
</script>
