<template>
  <div class="date-text">{{ day }} <br />{{ hour }}</div>
</template>
<script>
export default {
  props: ['date'],
  computed: {
    day() {
      if (this.date === null) {
        return '';
      }
      return this.date.split(' ')[0];
    },
    hour() {
      if (this.date === null) {
        return '';
      }
      return this.date.split(' ')[1];
    }
  }
};
</script>

<style lang="less" scoped>
.date-text {
  width: 100px;
  height: auto;
  text-align: center;
}
</style>