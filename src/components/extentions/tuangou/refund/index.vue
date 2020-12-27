<style lang="less" scoped>
.red {
  color: red;
}
</style>
<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">退款订单</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div style="padding-bottom: 15px">
        <p>请拿着支付订单号到相应的支付平台操作退款。</p>
      </div>
      <Table :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID"></TableItem>
        <TableItem prop="oid" title="支付订单号"></TableItem>
        <TableItem title="支付方式">
          <template slot-scope="{ data }">
            <p v-for="record in data.system_order.paid_records" :key="record.id">{{ record.paid_type_text }}：{{ record.paid_total }}元</p>
          </template>
        </TableItem>
        <TableItem title="用户">
          <template slot-scope="{ data }">
            <span v-if="data.user">{{ data.user.nick_name }}</span>
            <span class="red" v-else>用户不存在</span>
          </template>
        </TableItem>
        <TableItem title="价格">
          <template slot-scope="{ data }">{{ data.charge }}元</template>
        </TableItem>
        <TableItem title="状态">
          <template slot-scope="{ data }">{{ data.status_text }}</template>
        </TableItem>
        <TableItem title="操作">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.TuanGou.refund.handle" v-if="data.status === 0" text="已退款" @click="refundAction(data)"></p-del-button>
          </template>
        </TableItem>
      </Table>

      <Pagination class="mt-10" v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
    </div>
  </div>
</template>
<script>
export default {
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
      R.Extentions.tuanGou.Refunds.Index(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.pagination.page = resp.data.data.current_page;
        this.pagination.size = resp.data.data.per_page;
        this.loading = false;
      });
    },
    refundAction(item) {
      R.Extentions.tuanGou.Refunds.Handler({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    }
  }
};
</script>
