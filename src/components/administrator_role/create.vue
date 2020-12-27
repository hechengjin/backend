<style lang="less">
.permissions-box {
  width: 100%;
  height: auto;
  float: left;

  .permission-item {
    width: 100%;
    height: auto;
    float: left;
    margin-bottom: 10px;

    .title {
      width: 100%;
      height: auto;
      float: left;
      font-size: 18px;
      font-weight: bold;
      color: #333;
      margin-bottom: 10px;
    }

    .content {
      width: 100%;
      height: auto;
      float: left;

      .h-checkbox {
        margin-right: 10px;
        margin-bottom: 10px;
      }
    }
  }
}
</style>
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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :labelWidth="110" :rules="rules" :model="role">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="角色名" prop="display_name">
              <input type="text" v-model="role.display_name" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="Slug" prop="slug">
              <input type="text" v-model="role.slug" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="描述" prop="description">
              <input type="text" v-model="role.description" />
            </FormItem>
          </Cell>
        </Row>
        <Row :space="10">
          <Cell :width="24">
            <div class="permissions-box">
              <div class="permission-item" v-for="(items, title) in permissions" :key="title">
                <div class="title">{{ title }}</div>
                <div class="content">
                  <Checkbox v-model="role.permission_ids" :value="item.id" v-for="item in items" :key="item.id">{{ item.display_name }}</Checkbox>
                </div>
              </div>
            </div>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
import Role from 'model/AdministratorRole';

export default {
  data() {
    return {
      role: Role.parse({}),
      rules: {
        required: ['display_name', 'slug', 'description']
      },
      permissions: []
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.AdministratorRole.Create().then(res => {
        let data = res.data.permissions;
        this.permissions = data;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.role);
      }
    }
  }
};
</script>
