<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">积分变动</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">确认操作</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :labelWidth="110" :rules="rules" :model="record">
        <FormItem label="变动金额" prop="credit1">
          <template v-slot:label>变动金额</template>
          <input type="number" v-model="record.credit1" />
        </FormItem>
        <FormItem label="变动说明" prop="remark">
          <template v-slot:label>变动说明</template>
          <textarea v-model="record.remark" rows="2"></textarea>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      record: {
        credit1: 0,
        remark: null
      },
      rules: {
        required: ['credit1', 'remark']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let data = this.record;
        data.user_id = this.id;
        this.$emit('success', data);
      }
    }
  }
};
</script>
