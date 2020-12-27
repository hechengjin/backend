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
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="steps">
        <FormItem label="学习路径" prop="path_id">
          <template v-slot:label>学习路径</template>
          <Select v-model="steps.path_id" :datas="paths" keyName="id" titleName="name" :filterable="true"></Select>
        </FormItem>
        <FormItem label="步骤名" prop="name">
          <template v-slot:label>步骤名</template>
          <input type="text" v-model="steps.name" />
        </FormItem>
        <FormItem label="描述" prop="desc">
          <template v-slot:label>描述</template>
          <textarea v-model="steps.desc"></textarea>
        </FormItem>
        <FormItem label="升序" prop="sort">
          <template v-slot:label>升序</template>
          <input type="number" v-model="steps.sort" />
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  mounted() {
    R.Extentions.learningPaths.Steps.Create().then(res => {
      this.paths = res.data.paths;
      this.courses = res.data.courses;
    });
  },
  data() {
    return {
      steps: {
        name: '',
        sort: 0,
        decs: '',
        path_id: null
      },
      rules: {
        required: ['name', 'desc', 'sort', 'path_id']
      },
      paths: [],
      courses: []
    };
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        this.$emit('success', this.steps);
      }
    }
  }
};
</script>
