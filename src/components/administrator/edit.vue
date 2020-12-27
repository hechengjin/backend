<style lang="less"></style>
<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="save">保存</Button>
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
            <FormItem label="密码" prop="password">
              <input type="text" v-model="administrator.password" placeholder="不修改请勿填写" />
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
  props: ['id'],
  data() {
    return {
      administrator: Administrator.parse({}),
      rules: {
        required: ['name', 'email']
      },
      roles: []
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Administrator.Edit({ id: this.id }).then(resp => {
        this.administrator = resp.data;
        this.administrator.password = '';
      });

      R.Administrator.Create({ id: this.id }).then(resp => {
        this.roles = resp.data.roles;
      });
    },
    save() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.administrator);
      }
    }
  }
};
</script>
