<style lang="less">
.avatar {
  border-radius: 50%;
}
.green {
  color: green;
}
</style>
<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">团列表</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Table :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID" :width="100"></TableItem>
        <TableItem title="团长" :wdith="150">
          <template slot-scope="{ data }">
            <span>{{ data.create_user_name }}</span>
          </template>
        </TableItem>
        <TableItem title="成员" :width="100">
          <template slot-scope="{ data }">
            <span>{{ data.users.length }}人</span>
          </template>
        </TableItem>
        <TableItem prop="status_text" title="状态" :width="80"></TableItem>
        <TableItem title="人数" :width="200">
          <template slot-scope="{ data }">
            <span>{{ data.people_num - data.over_people_num }}</span>
            /
            <span>{{ data.people_num }}</span>
          </template>
        </TableItem>
        <TableItem prop="expired_at" title="过期" :width="300"></TableItem>
        <TableItem title="操作" align="center" :width="100">
          <template slot-scope="{ data }">
            <p-del-button
              permission="addons.TuanGou.goods.complete"
              v-if="data.status === 3"
              text="改为已完成"
              @click="completeAction(datas, data)"
            ></p-del-button>
          </template>
        </TableItem>
      </Table>

      <Pagination class="mt-10" align="right" v-model="pagination" @change="changePage" />
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
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
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
      data.id = this.id;
      R.Extentions.tuanGou.Goods.Items(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
      });
    },
    completeAction(datas, data) {
      R.Extentions.tuanGou.Goods.CompleteItem({ id: data.id }).then(res => {
        HeyUI.$Message.success('操作成功');
        this.getData();
      });
    }
  }
};
</script>
