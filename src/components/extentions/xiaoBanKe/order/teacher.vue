<template>
  <div style="padding: 15px;">
    <Form v-width="400" mode="block" ref="form">
      <FormItem>
        <template v-slot:label>老师</template>
        <Select
          v-model="teacher_id"
          :datas="teachers"
          keyName="id"
          titleName="name"
          :filterable="true"
        ></Select>
      </FormItem>
      <FormItem>
        <Button color="primary" @click="create">配置</Button>
      </FormItem>
    </Form>
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
