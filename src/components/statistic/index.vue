<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">统计工具</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          permission="statistic.userRegister"
          text="每日会员注册统计"
          @click="showPage('member')"
        ></p-button>
      </div>

      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          permission="statistic.courseSell"
          text="课程每日销售数量"
          @click="showPage('courseSell')"
        ></p-button>

        <p-button
          glass="h-btn h-btn-primary"
          permission="statistic.courseWatchDuration"
          text="每日课程观看时长"
          @click="showPage('courseWatchDuration')"
        ></p-button>
      </div>

      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          permission="statistic.orderCreated"
          text="每日订单创建量统计"
          @click="showPage('order')"
        ></p-button>

        <p-button
          glass="h-btn h-btn-primary"
          permission="statistic.orderPaidCount"
          text="每日已支付订单数"
          @click="showPage('orderPaidCount')"
        ></p-button>

        <p-button
          glass="h-btn h-btn-primary"
          permission="statistic.orderPaidSum"
          text="每日订单支付总额"
          @click="showPage('orderPaidSum')"
        ></p-button>

        <p-button
          glass="h-btn h-btn-primary"
          permission="statistic.roleSell"
          text="VIP每日销售数量"
          @click="showPage('roleSell')"
        ></p-button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {};
  },
  methods: {
    showPage(page) {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['./' + page], resolve);
          }
        }
      });
    }
  }
};
</script>
