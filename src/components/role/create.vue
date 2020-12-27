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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="role">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="角色名" prop="name">
              <input type="text" v-model="role.name" placeholder="如：年度会员" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="天数" prop="expire_days">
              <input type="number" v-model="role.expire_days" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="价格" prop="charge">
              <input type="number" v-model="role.charge" />
            </FormItem>
          </Cell>
        </Row>
        <Row>
          <Cell :width="24">
            <FormItem label="显示" prop="is_show">
              <h-switch v-model="role.is_show" :trueValue="1" :falseValue="0">
                <span slot="1">是</span>
                <span slot="0">否</span>
              </h-switch>
            </FormItem>
          </Cell>
        </Row>

        <Row>
          <Cell :width="24">
            <FormItem label="描述" prop="description">
              <textarea v-model="role.description" rows="3" placeholder="一行一个描述"></textarea>
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
import Role from 'model/Role';

export default {
  data() {
    return {
      role: Role.parse({}),
      rules: {
        required: ['name', 'description', 'expire_days', 'charge']
      }
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.role.is_show = 1;
      this.role.weight = 0;
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Role.Store(this.role).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
