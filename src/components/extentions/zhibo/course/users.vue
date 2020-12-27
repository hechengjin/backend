<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">订阅用户</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem title="用户ID" prop="user_id" :width="100"></TableItem>
          <TableItem title="用户">
            <template slot-scope="{ data }">
              <span v-if="data.user">{{ data.user.nick_name }}</span>
              <span class="c-red" v-else>已删除</span>
            </template>
          </TableItem>
          <TableItem title="价格">
            <template slot-scope="{ data }">
              <span v-if="data.is_vip === 1" class="c-red">VIP免费</span>
              <span v-else>￥{{ data.charge }}</span>
            </template>
          </TableItem>
          <TableItem title="订阅时间" prop="created_at" :width="150"></TableItem>
        </Table>
      </div>

      <div class="float-box mb-10">
        <Pagination class="mt-10" align="right" v-model="pagination" @change="changePage" />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['course_id'],
  data() {
    return {
      datas: [],
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      filer: {
        user_id: null
      },
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    changePage() {
      this.getData();
    },
    getData(reset = false) {
      if (reset) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      Object.assign(data, {
        id: this.course_id
      });
      R.Extentions.zhibo.Course.Users(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
      });
    }
  }
};
</script>
