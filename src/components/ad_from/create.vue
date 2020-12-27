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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="adfrom">
        <Row :space="10">
          <Cell :width="12">
            <FormItem label="Name" prop="from_name">
              <input type="text" v-model="adfrom.from_name" />
            </FormItem>
          </Cell>
          <Cell :width="12">
            <FormItem label="Key" prop="from_key">
              <input type="text" v-model="adfrom.from_key" />
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
import AdFrom from 'model/AdFrom';

export default {
  data() {
    return {
      adfrom: AdFrom.parse({}),
      rules: {
        required: ['from_name', 'from_key']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.AdFrom.Store(this.adfrom).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
