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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="teacher">
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
        <FormItem label="邮箱" prop="username">
          <template v-slot:label>邮箱</template>
          <input type="text" v-model="teacher.username" />
          <warn text="用于教师登录"></warn>
        </FormItem>
        <FormItem label="密码" prop="password">
          <template v-slot:label>密码</template>
          <input type="text" v-model="teacher.password" />
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
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
        required: ['name', 'avatar', 'short_desc', 'username', 'password']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.xiaoBanKe.Teacher.Store(this.teacher).then(resp => {
          this.$emit('success');
        });
      }
    }
  }
};
</script>
