<style lang="less"></style>
<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :labelWidth="110" :rules="rules" :model="administrator">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="角色" prop="role_id">
              <Select
                v-model="administrator.role_id"
                :datas="roles"
                keyName="id"
                titleName="display_name"
                :filterable="true"
                :multiple="true"
              ></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="姓名" prop="name">
              <input type="text" v-model="administrator.name" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="邮箱" prop="email">
              <input type="text" v-model="administrator.email" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="密码" prop="password">
              <input type="text" v-model="administrator.password" />
            </FormItem>
          </Cell>
        </Row>

        <FormItem label="禁止登录" prop="is_ban_login">
          <h-switch v-model="administrator.is_ban_login" :trueValue="1" :falseValue="0"></h-switch>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import Administrator from 'model/Administrator';

export default {
  data() {
    return {
      administrator: Administrator.parse({}),
      rules: {
        required: ['name', 'email', 'password']
      },
      roles: []
    };
  },
  mounted() {
    R.Administrator.Create().then(res => {
      this.roles = res.data.roles;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let data = this.administrator;
        data.password_confirmation = data.password;
        this.$emit('success', data);
      }
    }
  }
};
</script>
