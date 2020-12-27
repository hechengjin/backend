<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">团购订单</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="8">
              <FormItem label="关键字">
                <input type="text" v-model="filter.keywords" placeholder="请输入商品名关键字" />
              </FormItem>
            </Cell>
            <Cell :width="8">
              <FormItem label="用户ID">
                <input type="text" v-model="filter.user_id" placeholder="用户ID" />
              </FormItem>
            </Cell>
            <Cell :width="8">
              <FormItem label="状态">
                <Select v-model="filter.status" :datas="status" keyName="id" titleName="title"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData">搜索</Button>
                <Button class="h-btn" @click="reset">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem title="商品ID" :width="80">
            <template slot-scope="{ data }">
              <span v-if="data.goods">{{ data.goods.other_id }}</span>
              <span class="red" v-else>已删除</span>
            </template>
          </TableItem>
          <TableItem prop="user_id" title="用户ID" :width="80"></TableItem>
          <TableItem title="商品">
            <template slot-scope="{ data }">
              <span v-if="data.goods">{{ data.goods.goods_title }}</span>
              <span class="red" v-else>已删除</span>
            </template>
          </TableItem>
          <TableItem title="用户" :width="150">
            <template slot-scope="{ data }">
              <span v-if="data.user">{{ data.user.nick_name }}</span>
              <span class="red" v-else>不存在</span>
            </template>
          </TableItem>
          <TableItem title="支付" :width="80">
            <template slot-scope="{ data }">￥{{ data.charge }}</template>
          </TableItem>
          <TableItem title="状态" :width="80">
            <template slot-scope="{ data }">
              <span v-if="data.status_text === '已支付'">已支付</span>
              <span v-else class="red">未支付</span>
            </template>
          </TableItem>
          <TableItem title="时间" :width="120">
            <template slot-scope="{ data }">{{ data.created_at }}</template>
          </TableItem>
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
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      datas: [],
      loading: false,
      status: [
        {
          id: null,
          title: '全部'
        },
        {
          id: 0,
          title: '未支付'
        },
        {
          id: 1,
          title: '已支付'
        }
      ],
      filter: {
        keywords: null,
        status: null,
        user_id: null
      }
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    changePage() {
      this.getData(false);
    },
    reset() {
      this.filter.keywords = null;
      this.filter.status = null;
      this.filter.user_id = null;
      this.getData(true);
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = Object.assign(this.pagination, this.filter);
      R.Extentions.tuanGou.Orders.Index(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
      });
    }
  }
};
</script>
