<style lang="less" scoped>
.copytext {
  cursor: pointer;
}
</style>
<template>
  <span class="copytext" @click="copy">{{copytext}}</span>
</template>
<script>
export default {
  props: ['copytext'],
  methods: {
    copy() {
      this.$Clipboard({
        text: this.copytext
      });
    }
  }
};
</script>