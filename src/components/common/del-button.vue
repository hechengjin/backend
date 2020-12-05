<template>
  <Poptip content="确认操作？" @confirm="$emit('click')" v-if="inPermission">
    <Button class="h-btn h-btn-s h-btn-red" :class="glass">{{text || '删除'}}</Button>
  </Poptip>
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
  }
};
</script>
