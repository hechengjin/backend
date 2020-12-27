<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="paths">
        <FormItem label="路径名" prop="name">
          <template v-slot:label>路径名</template>
          <input type="text" v-model="paths.name" />
        </FormItem>
        <FormItem label="封面" prop="thumb">
          <template v-slot:label>封面</template>
          <image-upload v-model="paths.thumb" name="封面"></image-upload>
        </FormItem>
        <FormItem label="原价" prop="original_charge">
          <template v-slot:label>原价</template>
          <div class="h-input-group" v-width="200">
            <input type="text" v-model="paths.original_charge" />
            <span class="h-input-addon">元</span>
          </div>
        </FormItem>
        <FormItem label="现价" prop="charge">
          <template v-slot:label>现价</template>
          <div class="h-input-group" v-width="200">
            <input type="text" v-model="paths.charge" />
            <span class="h-input-addon">元</span>
          </div>
        </FormItem>
        <FormItem label="描述" prop="desc">
          <template v-slot:label>描述</template>
          <textarea v-model="paths.desc"></textarea>
        </FormItem>
        <FormItem label="显示" prop="is_show">
          <template v-slot:label>显示</template>
          <h-switch v-model="paths.is_show" :trueValue="1" :falseValue="0"></h-switch>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  mounted() {
    R.Extentions.learningPaths.Paths.Edit({ id: this.id }).then(res => {
      this.paths = res.data;
    });
  },
  data() {
    return {
      paths: {
        name: '',
        is_show: 1,
        thumb: '',
        charge: 0,
        original_charge: 0,
        desc: ''
      },
      rules: {
        required: ['name', 'is_show', 'thumb', 'charge', 'original_charge', 'desc']
      }
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.paths);
      }
    }
  }
};
</script>
