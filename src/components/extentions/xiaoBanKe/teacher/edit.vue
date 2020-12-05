<template>
  <div style="padding: 15px;">
    <Form
      v-width="400"
      mode="block"
      ref="form"
      :validOnChange="true"
      :showErrorTip="true"
      :rules="rules"
      :model="teacher"
    >
      <FormItem label="讲师名" prop="name">
        <template v-slot:label>讲师名</template>
        <input type="text" v-model="teacher.name" />
      </FormItem>
      <FormItem label="头像" prop="avatar">
        <template v-slot:label>头像</template>
        <image-upload v-model="teacher.avatar" name="头像"></image-upload>
      </FormItem>
      <FormItem label="简介" prop="short_desc">
        <template v-slot:label>简介</template>
        <textarea v-model="teacher.short_desc"></textarea>
      </FormItem>
      <FormItem label="密码" prop="password">
        <template v-slot:label>密码</template>
        <input type="text" v-model="teacher.password" />
      </FormItem>
      <FormItem>
        <Button color="primary" @click="create">保存</Button>
      </FormItem>
    </Form>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      teacher: {
        name: '',
        avatar: '',
        short_desc: '',
        username: '',
        password: ''
      },
      rules: {
        required: ['name', 'avatar', 'short_desc', 'password']
      }
    };
  },
  mounted() {
    R.Extentions.xiaoBanKe.Teacher.Edit({ id: this.id }).then(res => {
      this.teacher = res.data.data;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.xiaoBanKe.Teacher.Update(this.teacher).then(resp => {
          this.$emit('success');
        });
      }
    }
  }
};
</script>
