<template>
  <div class="table-basic-vue frame-page h-panel h-panel-margin-0 w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">订阅记录</span>
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
              <FormItem label="订阅时间">
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
        <p-button glass="h-btn h-btn-primary" permission="course.subscribe.create" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="data">
          <TableItem title="课程ID" prop="course_id" :width="120"></TableItem>
          <TableItem title="用户ID" prop="user_id" :width="120"></TableItem>
          <TableItem title="用户" :width="150">
            <template slot-scope="{ data }">
              <span v-if="typeof users[data.user_id] !== 'undefined'">{{ users[data.user_id].nick_name }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem title="订阅时间" :width="120">
            <template slot-scope="{ data }">
              <date-text :date="data.created_at"></date-text>
            </template>
          </TableItem>
          <TableItem title="操作" :width="120">
            <template slot-scope="{ data }">
              <p-del-button permission="course.subscribe.delete" @click="remove(data)"></p-del-button>
            </template>
          </TableItem>
        </Table>
      </div>

      <div class="float-box mt-10">
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
      data: [],
      users: [],
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      loading: false,
      filter: {
        user_id: null,
        subscribe_start_at: null,
        subscribe_end_at: null
      },
      dateRange: {}
    };
  },
  mounted() {
    this.getData(true);
  },
  watch: {
    dateRange() {
      this.filter.subscribe_start_at = this.dateRange.start;
      this.filter.subscribe_end_at = this.dateRange.end;
    }
  },
  methods: {
    reset() {
      this.filter.user_id = null;
      this.filter.subscribe_start_at = null;
      this.filter.subscribe_end_at = null;
      this.dateRange = {};
      this.getData(true);
    },
    getData(reset = false) {
      if (reset) {
        this.pagination.page = 1;
      }

      this.loading = true;
      let data = this.pagination;
      data.course_id = this.id;
      Object.assign(data, this.filter);
      R.Course.Subscribes(data).then(res => {
        this.data = res.data.data.data;
        this.users = res.data.users;
        this.pagination.total = res.data.data.total;
        this.loading = false;
      });
    },
    changePage() {
      this.getData();
    },
    remove(item) {
      R.Course.SubscribeDelete({ course_id: this.id, user_id: item.user_id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    create() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./create'], resolve);
          },
          datas: {
            id: this.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData(true);
          }
        }
      });
    }
  }
};
</script>