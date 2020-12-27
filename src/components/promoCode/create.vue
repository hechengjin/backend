<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">创建</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="PromoCode">
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="优惠码" prop="code">
              <input type="text" v-model="PromoCode.code" placeholder="请勿使用U或者u开头" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="到期时间" prop="expired_at">
              <DatePicker v-model="PromoCode.expired_at" type="datetime"></DatePicker>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="抵扣金额" prop="invited_user_reward">
              <input type="number" v-model="PromoCode.invited_user_reward" placeholder="要求：整数。单位：元" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="可使用次数" prop="use_times">
              <input type="number" v-model="PromoCode.use_times" placeholder="0=不限制" />
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
import PromoCode from 'model/PromoCode';

export default {
  data() {
    return {
      PromoCode: PromoCode.parse({}),
      rules: {
        required: ['code', 'expired_at', 'invited_user_reward', 'use_times']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.PromoCode.Create(this.PromoCode).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
