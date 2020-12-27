<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">统计</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box" v-if="data">
        <line-chart :height="200" :chart-data="data"></line-chart>
      </div>
    </div>
  </div>
</template>
<script>
import LineChart from 'components/common/chartjs/line';

export default {
  components: {
    LineChart
  },
  props: ['id'],
  data() {
    return {
      adfrom: null,
      data: null
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.AdFrom.Number({ id: this.id }).then(resp => {
        this.adfrom = resp.data.ad;
        let data = {
          labels: resp.data.labels,
          datasets: [
            {
              label: '统计',
              data: resp.data.dataset
            }
          ]
        };
        this.data = data;
      });
    }
  }
};
</script>
