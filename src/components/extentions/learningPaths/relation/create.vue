<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="relation">
        <FormItem label="课程类型" prop="type">
          <template v-slot:label>课程类型</template>
          <Select v-model="relation.type" :datas="types" keyName="value" titleName="key"></Select>
        </FormItem>

        <FormItem label="课程" prop="other_id">
          <template v-slot:label>课程</template>
          <Select v-model="relation.other_id" :datas="lists" keyName="id" titleName="name"></Select>
        </FormItem>

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
        step_id: this.id,
        name: null,
        thumb: null,
        charge: null,
        sort: null,
        other_id: null,
        type: null
      },
      rules: {
        required: ['step_id', 'type', 'name', 'thumb', 'charge', 'sort', 'other_id']
      },
      types: [],
      lists: []
    };
  },
  mounted() {
    R.Extentions.learningPaths.Relation.Create({ step_id: this.id }).then(resp => {
      this.types = resp.data.types;
    });
  },
  watch: {
    'relation.type'() {
      this.relation.other_id = null;
      this.relation.name = null;
      this.relation.thumb = null;
      this.relation.charge = null;

      R.Extentions.learningPaths.Relation.Create({ step_id: this.id, type: this.relation.type }).then(resp => {
        this.lists = resp.data.data;
      });
    },
    'relation.other_id'() {
      for (let i = 0; i < this.lists.length; i++) {
        if (this.lists[i].id === this.relation.other_id) {
          let data = this.lists[i];
          this.relation.name = data.name;
          this.relation.charge = data.charge;
          this.relation.thumb = data.thumb;
          break;
        }
      }
    }
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.Extentions.learningPaths.Relation.Store(this.relation).then(() => {
          this.$emit('success');
        });
      }
    }
  }
};
</script>
