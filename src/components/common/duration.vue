<style lang="less" scoped>
.duration {
  width: 100%;
  height: auto;
  float: left;

  .hour,
  .minute,
  .second {
    display: block;
    width: 90px;
    height: @input-height;
    float: left;

    input {
      width: 100%;
      height: @input-height;
      box-sizing: border-box;
      border: 1px solid #f2f2f2;
      line-height: @input-height;
      text-align: center;
      border-radius: 0;
    }
  }

  .unit {
    display: block;
    width: 50px;
    height: @input-height;
    line-height: @input-height;
    float: left;
    font-size: 15px;
    color: #333;
    text-align: center;
    background-color: rgba(0, 0, 0, 0.06);
    border-top: 1px solid #f2f2f2;
    border-bottom: 1px solid #f2f2f2;
  }
}
</style>
<template>
  <div class="duration">
    <div class="hour">
      <input type="text" v-model="hour" min="0" placeholder="时" />
    </div>
    <div class="unit">时</div>
    <div class="minute">
      <input type="text" v-model="minute" min="0" placeholder="分" />
    </div>
    <div class="unit">分</div>
    <div class="second">
      <input type="text" v-model="second" min="0" placeholder="秒" />
    </div>
    <div class="unit">秒</div>
  </div>
</template>
<script>
export default {
  props: ['value'],
  data() {
    return {
      hour: 0,
      minute: 0,
      second: 0,
      seconds: 0
    };
  },
  watch: {
    value(newVal) {
      this.seconds = parseInt(newVal);
    },
    seconds(newVal) {
      this.dateParse();
      this.$emit('input', parseInt(newVal));
    },
    hour() {
      this.seconds = parseInt(this.hour * 3600) + parseInt(this.minute * 60) + parseInt(this.second);
    },
    minute() {
      this.seconds = parseInt(this.hour * 3600) + parseInt(this.minute * 60) + parseInt(this.second);
    },
    second() {
      this.seconds = parseInt(this.hour * 3600) + parseInt(this.minute * 60) + parseInt(this.second);
    }
  },
  methods: {
    dateParse() {
      this.hour = parseInt(this.seconds / 3600);
      this.minute = parseInt((this.seconds - this.hour * 3600) / 60);
      this.second = parseInt(this.seconds - this.hour * 3600 - this.minute * 60);
    }
  }
};
</script>