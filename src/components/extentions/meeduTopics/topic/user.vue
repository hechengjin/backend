<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">订单</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="12">
              <FormItem label="UID">
                <user-filter v-model="filter.user_id"></user-filter>
              </FormItem>
            </Cell>
            <Cell :width="12">
              <FormItem>
                <Button color="primary" @click="getData(true)">过滤</Button>
                <Button @click="reset()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem title="用户ID" prop="user_id" :width="80"></TableItem>
          <TableItem title="用户">
            <template slot-scope="{ data }">
              <span v-if="data.user">{{ data.user.nick_name }}</span>
              <span v-else class="c-red">已删除</span>
            </template>
          </TableItem>
          <TableItem prop="charge" title="价格" unit="元"></TableItem>
        </Table>
      </div>

      <div class="float-box mb-10">
        <Pagination class="mt-10" v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      filter: {
        user_id: null
      },
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.filter.user_id = null;
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      data.topic_id = this.id;
      data.user_id = this.filter.user_id;
      R.Extentions.meeduTopics.Order.Index(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
      });
    }
  }
};
</script>
