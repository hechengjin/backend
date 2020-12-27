<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="relation">
        <template v-if="relation.other_id">
          <FormItem label="课程名" prop="name">
            <template v-slot:label>课程名</template>
            <input type="text" v-model="relation.name" />
          </FormItem>
          <FormItem label="课程封面" prop="thumb">
            <template v-slot:label>课程封面</template>
            <image-upload v-model="relation.thumb" name="课程封面"></image-upload>
          </FormItem>
          <FormItem label="课程价格" prop="charge">
            <template v-slot:label>课程价格</template>
            <div class="h-input-group" v-width="200">
              <input type="text" v-model="relation.charge" />
              <span class="h-input-addon">元</span>
            </div>
          </FormItem>
        </template>

        <FormItem label="升序" prop="sort">
          <template v-slot:label>升序</template>
          <div class="h-input-group" v-width="200">
            <input type="text" v-model="relation.sort" />
          </div>
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
      relation: {
        name: null,
        thumb: null,
        charge: null,
        sort: null,
        other_id: null,
        type: null
      },
      rules: {
        required: ['type', 'name', 'thumb', 'charge', 'sort', 'other_id']
      },
      types: [],
      lists: []
    };
  },
  mounted() {
    R.Extentions.learningPaths.Relation.Edit({ id: this.id }).then(resp => {
      this.relation = resp.data.data;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.learningPaths.Relation.Update(this.relation).then(() => {
          this.$emit('success');
        });
      }
    }
  }
};
</script>
