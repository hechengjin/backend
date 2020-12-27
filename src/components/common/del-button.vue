<template>
  <Button v-if="inPermission" class="h-btn h-btn-s h-btn-red" @click="run" :class="glass">{{ text || '删除' }}</Button>
</template>
<script>
export default {
  props: ['permission', 'isSuper', 'text', 'glass'],
  computed: {
    inPermission() {
      if (typeof this.$store.state.User.permissions === 'undefined') {
        return false;
      }
      let permissions = this.$store.state.User.permissions;
      return typeof permissions[this.permission] !== 'undefined';
    }
  },
  methods: {
    run() {
      this.$Confirm('确认操作？', '警告').then(() => {
        this.$emit('click');
      });
    }
  }
};
</script>
