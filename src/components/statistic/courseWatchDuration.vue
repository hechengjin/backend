<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">每日课程观看时长</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form :labelWidth="110">
          <Row :space="10">
            <Cell :width="12">
              <FormItem label="课程">
                <Select v-model="course_id" :datas="courses" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="时间范围">
                <DateRangePicker v-model="daterange"></DateRangePicker>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData(true)">过滤</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
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
      data: {},
      daterange: {
        start: null,
        end: null
      },
      course_id: null,
      courses: []
    };
  },
  mounted() {
    R.Course.All().then(res => {
      this.courses = res.data.data;
    });
    this.getData();
  },
  methods: {
    getData() {
      let data = {};
      if (this.daterange.start) {
        data.start_at = this.daterange.start;
      }
      if (this.daterange.end) {
        data.end_at = this.daterange.end;
      }
      data.course_id = this.course_id;
      R.Statistic.courseWatchDuration(data).then(resp => {
        let data = {
          labels: resp.data.labels,
          datasets: [
            {
              label: '时长(时)',
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
