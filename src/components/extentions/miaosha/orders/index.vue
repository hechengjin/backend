<style lang="less" scoped>
.red {
  color: red;
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">订单</span>
    </div>
    <div class="h-panel-body">
      <Form>
        <Row :space="10">
          <Cell :width="6">
            <FormItem label="课程">
              <Select v-model="filter.type" :filterable="true" :datas="types" keyName="goods_type" titleName="goods_type_text"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="课程">
              <Select v-model="filter.gid" :filterable="true" :datas="goodsList" keyName="id" titleName="goods_title"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="支付状态">
              <Select v-model="filter.status" :filterable="true" :datas="statusMap" keyName="id" titleName="text"></Select>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem>
              <Button color="primary" @click="getData(true)">搜索</Button>
              <Button class="h-btn" @click="reset">重置</Button>
            </FormItem>
          </Cell>
        </Row>
      </Form>

      <Table :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID" :width="80"></TableItem>
        <TableItem title="商品ID" :width="80">
          <template slot-scope="{ data }">
            <span v-if="data.goods">{{ data.goods.goods_id }}</span>
            <span class="red" v-else>已删除</span>
          </template>
        </TableItem>
        <TableItem prop="user_id" title="用户ID" :width="80"></TableItem>
        <TableItem title="用户" :width="120">
          <template slot-scope="{ data }">
            <span v-if="data.user">{{ data.user.nick_name }}</span>
            <span class="red" v-else>已删除</span>
          </template>
        </TableItem>
        <TableItem title="类型" :width="100">
          <template slot-scope="{ data }">
            <span v-if="data.goods">{{ data.goods.goods_type_text }}</span>
            <span class="red" v-else>已删除</span>
          </template>
        </TableItem>
        <TableItem title="商品">
          <template slot-scope="{ data }">
            <span v-if="data.goods">{{ data.goods.goods_title }}</span>
            <span class="red" v-else>已删除</span>
          </template>
        </TableItem>
        <TableItem title="秒杀价" :width="80">
          <template slot-scope="{ data }">{{ data.charge }}元</template>
        </TableItem>

        <TableItem title="状态" :width="80">
          <template slot-scope="{ data }">
            <span v-if="data.status === 1">已支付</span>
            <span class="red" v-else>未支付</span>
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
      loading: false,
      filter: {
        type: null,
        gid: null,
        status: -1
      },
      statusMap: [
        {
          id: -1,
          text: '全部'
        },
        {
          id: 0,
          text: '未支付'
        },
        {
          id: 1,
          text: '已支付'
        }
      ],
      goods: [],
      types: []
    };
  },
  mounted() {
    this.init();
  },
  computed: {
    goodsList() {
      if (!this.filter.type) {
        return [];
      }
      let data = [];
      for (let i = 0; i < this.goods.length; i++) {
        if (this.goods[i].goods_type === this.filter.type) {
          data.push(this.goods[i]);
        }
      }

      return data;
    }
  },
  methods: {
    reset() {
      this.filter = {
        gid: null,
        status: -1
      };
      this.getData();
    },
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
      let data = Object.assign(this.filter, this.pagination);
      R.Extentions.miaoSha.Orders.Index(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.goods = resp.data.goods;
        this.loading = false;
        this.types = resp.data.types;
      });
    }
  }
};
</script>
