<style lang="less">
.avatar {
  border-radius: 9px;
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">订单</span>
    </div>
    <div class="h-panel-body">
      <div class="mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="UID">
                <user-filter v-model="cond.user_id"></user-filter>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="订单号">
                <input type="text" v-model="cond.order_id" placeholder="订单号" />
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="状态">
                <Select v-model="cond.status" :datas="statusArr"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData(true)">搜索</Button>
                <Button @click="reset">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <Table :loading="loading" :datas="datas" :stripe="true">
        <TableItem prop="id" title="ID" :width="100"></TableItem>
        <TableItem prop="user_id" title="用户ID" :width="100"></TableItem>
        <TableItem title="用户" :width="120">
          <template slot-scope="{ data }">
            <span v-if="users[data.user_id]">{{ users[data.user_id].nick_name }}</span>
            <span v-else class="red">已删除</span>
          </template>
        </TableItem>
        <TableItem title="订单号" :width="120">
          <template slot-scope="{ data }">
            <copytext :copytext="data.order_id" />
          </template>
        </TableItem>
        <TableItem title="总价" :width="100">
          <template slot-scope="{ data }">
            <span>￥{{ data.charge }}</span>
          </template>
        </TableItem>
        <TableItem title="状态" :width="80">
          <template slot-scope="{ data }">
            <span v-if="data.status_text === '已支付'" class="red">已支付</span>
            <span v-else>{{ data.status_text }}</span>
          </template>
        </TableItem>
        <TableItem title="商品">
          <template slot-scope="{ data }">
            <ul>
              <li v-for="item in data.goods" :key="item.id">[{{ item.goods_text }}]{{ item.goods_name }}:￥{{ item.charge }}</li>
            </ul>
          </template>
        </TableItem>
        <TableItem title="时间" :width="120">
          <template slot-scope="{ data }">
            <date-text :date="data.created_at"></date-text>
          </template>
        </TableItem>
        <TableItem :width="240" title="操作" align="center">
          <template slot-scope="{ data }">
            <p-del-button
              permission="order.finish"
              text="改为已支付"
              @click="finishOrder(datas, data)"
              v-if="data.status === 1 || data.status === 5"
            ></p-del-button>
            <p-button glass="h-btn h-btn-s h-btn-primary" permission="order.detail" text="详情" @click="showDetail(data)"></p-button>
          </template>
        </TableItem>
      </Table>
      <p></p>
      <Pagination v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
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
      cond: {
        user_id: null,
        status: null,
        order_id: null
      },
      statusArr: [
        {
          title: '未支付',
          key: 1
        },
        {
          title: '支付中',
          key: 5
        },
        {
          title: '已支付',
          key: 9
        },
        {
          title: '已取消',
          key: 7
        }
      ],
      datas: [],
      loading: false,
      users: []
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    changePage() {
      this.getData();
    },
    reset() {
      this.cond.user_id = null;
      this.cond.status = null;
      this.cond.order_id = null;
      this.getData(true);
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      Object.assign(data, this.cond);
      R.Order.List(data).then(resp => {
        this.datas = resp.data.orders.data;
        this.pagination.total = resp.data.orders.total;
        this.loading = false;
        this.users = resp.data.users;
      });
    },
    finishOrder(orders, order) {
      R.Order.Finish(order).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(false);
      });
    },
    showDetail(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./detail'], resolve);
          },
          datas: {
            id: item.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
          }
        }
      });
    }
  }
};
</script>
