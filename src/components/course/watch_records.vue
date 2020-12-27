<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">观看记录</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="UID">
                <user-filter v-model="filter.user_id"></user-filter>
              </FormItem>
            </Cell>
            <Cell :width="10">
              <FormItem label="看完时间">
                <DateRangePicker v-model="dateRange" format="YYYY-MM-DD"></DateRangePicker>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button class="h-btn h-btn-primary" @click="getData(true)">搜索</Button>
                <Button class="h-btn" @click="reset()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="list">
          <TableItem title="课程ID" prop="course_id" :width="120"></TableItem>
          <TableItem title="用户ID" prop="user_id" :width="120"></TableItem>
          <TableItem title="用户" :width="150">
            <template slot-scope="{ data }">
              <span v-if="typeof users[data.user_id] !== 'undefined'">{{ users[data.user_id].nick_name }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem title="手机号" :width="120">
            <template slot-scope="{ data }">
              <span v-if="typeof users[data.user_id] !== 'undefined'">{{ users[data.user_id].mobile }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem title="观看进度" :width="100">
            <template slot-scope="{ data }">
              <span>{{ data.progress }}%</span>
            </template>
          </TableItem>
          <TableItem title="开始时间" :width="120">
            <template slot-scope="{ data }">
              <date-text :date="data.created_at"></date-text>
            </template>
          </TableItem>
          <TableItem title="看完时间" :width="120">
            <template slot-scope="{ data }">
              <date-text :date="data.watched_at"></date-text>
            </template>
          </TableItem>

          <TableItem title="订阅" :width="80">
            <template slot-scope="{ data }">
              <span v-if="typeof subscribeRecords[data.user_id] !== 'undefined'">是</span>
              <span v-else class="red">否</span>
            </template>
          </TableItem>
          <TableItem title="订阅" :width="100">
            <template slot-scope="{ data }">
              <Button class="h-btn h-btn-s h-btn-primary" @click="showDesc(data)">详情</Button>
            </template>
          </TableItem>
        </Table>
      </div>

      <div class="float-box mb-10">
        <Pagination align="right" v-model="pagination" @change="changePage" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['id'],
  data() {
    return {
      list: [],
      users: [],
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      loading: false,
      subscribeRecords: [],
      filter: {
        user_id: null,
        watched_start_at: null,
        watched_end_at: null
      },
      dateRange: {}
    };
  },
  mounted() {
    this.getData(true);
  },
  watch: {
    dateRange() {
      this.filter.watched_start_at = this.dateRange.start;
      this.filter.watched_end_at = this.dateRange.end;
    }
  },
  methods: {
    reset() {
      this.filter.user_id = null;
      this.filter.watched_start_at = null;
      this.filter.watched_end_at = null;
      this.dateRange = {};
      this.getData(true);
    },
    getData(reset = false) {
      if (reset) {
        this.pagination.page = 1;
      }

      this.loading = true;
      let data = this.pagination;
      data.id = this.id;
      Object.assign(data, this.filter);
      R.Course.WatchRecords(data).then(res => {
        this.list = res.data.data.data;
        this.users = res.data.users;
        this.pagination.total = res.data.data.total;
        this.subscribeRecords = res.data.subscribe_records;
        this.loading = false;
      });
    },
    changePage() {
      this.getData();
    },
    showDesc(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['../video/watch_records'], resolve);
          },
          datas: {
            id: 0,
            course_id: item.course_id,
            user_id: item.user_id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData(true);
          }
        }
      });
    },
    exportExcel() {
      let data = {
        video_id: 0,
        export: 1,
        course_id: this.id
      };
      R.Video.WatchRecords(data).then(res => {
        if (res.data.data.length === 1) {
          HeyUI.$Message.warn('数据为空');
          return;
        }
        let date = new Date();
        let filename = '课程观看记录|' + date.getFullYear() + '年' + date.getMonth() + '月' + date.getDate() + '日.xlsx';
        let sheetName = '默认';

        Utils.exportExcel(res.data.data, filename, sheetName);
      });
    }
  }
};
</script>
