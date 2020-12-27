<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">分销商品</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="9">
              <FormItem label="关键字">
                <input type="text" v-model="filter.keywords" placeholder="关键字搜索" />
              </FormItem>
            </Cell>
            <Cell :width="9">
              <FormItem label="商品类型">
                <Select v-model="filter.type" :filterable="true" :datas="types" keyName="value" titleName="name"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <Button color="primary" @click="getData(true)">搜索</Button>
              <Button @click="reset">重置</Button>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          icon="h-icon-plus"
          permission="addons.MultiLevelShare.goods.store"
          text="添加"
          @click="create()"
        ></p-button>
      </div>
      <div class="float-box">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem prop="goods_id" title="商品ID" :width="80"></TableItem>
          <TableItem prop="goods_type_text" title="类型" :width="80"></TableItem>
          <TableItem prop="goods_title" title="商品"></TableItem>
          <TableItem title="价格" :width="150">
            <template slot-scope="{ data }">
              <span>￥{{ data.goods_charge }}</span>
            </template>
          </TableItem>
          <TableItem title="奖励1/2/3" :width="200">
            <template slot-scope="{ data }">
              <span>￥{{ data.reward }}</span>
              /
              <span>￥{{ data.reward2 }}</span>
              /
              <span>￥{{ data.reward3 }}</span>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.MultiLevelShare.goods.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.MultiLevelShare.goods.edit" text="编辑" @click="edit(data)"></p-button>
            </template>
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
      courses: [],
      filter: {
        type: null,
        keywords: null
      },
      types: []
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.filter.type = null;
      this.filter.keywords = null;
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
      R.Extentions.multiLevelShare.Goods.List(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.types = resp.data.types;
        this.loading = false;
      });
    },
    create() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./create'], resolve);
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
    remove(data, item) {
      R.Extentions.multiLevelShare.Goods.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    },
    edit(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./edit'], resolve);
          },
          datas: {
            id: item.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData();
          }
        }
      });
    }
  }
};
</script>
