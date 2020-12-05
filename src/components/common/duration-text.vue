<template>
  <span>
    <template v-if="hour > 0">{{hour}}:{{minute}}:{{second}}</template>
    <template v-else>{{minute}}:{{second}}</template>
  </span>
</template>
<script>
export default {
  props: ['seconds'],
  data() {
    return {
      hour: 0,
      minute: 0,
      second: 0
    };
  },
  watch: {
    seconds(newVal) {
      this.parse(newVal);
    }
  },
  mounted() {
    this.parse(this.seconds);
  },
  methods: {
    parse(seconds) {
      let hours = parseInt(seconds / 3600);
      this.hour = hours < 10 ? '0' + hours : hours;

      let minute = parseInt((seconds - hours * 3600) / 60);
      this.minute = minute < 10 ? '0' + minute : minute;

      let second = seconds - hours * 3600 - minute * 60;
      this.second = second < 10 ? '0' + second : second;
    }
  }
};
</script>