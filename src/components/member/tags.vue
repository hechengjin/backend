<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">标签</span>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form">
        <FormItem label="标签">
          <TagInput v-model="user.tags" :limit="5" :wordlimit="20"></TagInput>
        </FormItem>
        <FormItem>
          <Button color="primary" @click="update">保存</Button>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id', 'tags'],
  data() {
    return {
      user: {
        id: this.id,
        tags: []
      }
    };
  },
  mounted() {
    let tags = [];
    this.tags.forEach(item => {
      tags.push(item.name);
    });
    this.user.tags = tags;
  },
  methods: {
    update() {
      R.Member.Tags(this.user).then(res => {
        this.$emit('success', this.user);
      });
    }
  }
};
</script>
