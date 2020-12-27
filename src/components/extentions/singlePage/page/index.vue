<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">单页</span>
    </div>
    <div class="h-panel-body">
      <div class="mb-10">
        <p-button glass="h-btn h-btn-primary" icon="h-icon-plus" permission="addons.SinglePage.page.store" text="添加" @click="create()"></p-button>
      </div>
      <Table :loading="loading" :datas="datas">
        <TableItem prop="sign" title="唯一标识"></TableItem>
        <TableItem prop="title" title="标题"></TableItem>
        <TableItem prop="url" title="地址"></TableItem>
        <TableItem prop="view_times" title="浏览次数"></TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.SinglePage.page.delete" @click="remove(datas, data)"></p-del-button>
            <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.SinglePage.page.update" text="编辑" @click="edit(data)"></p-button>
          </template>
        </TableItem>
      </Table>

      <Pagination class="mt-10" align="right" v-model="pagination" @change="changePage" />
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
      R.Extentions.singlePage.Page.List(this.pagination).then(resp => {
        this.datas = resp.data.data;
        this.pagination.total = resp.data.total;
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
            R.Extentions.singlePage.Page.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.singlePage.Page.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
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
            R.Extentions.singlePage.Page.Update(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    }
  }
};
</script>
