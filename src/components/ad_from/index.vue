<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">推广链接</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" permission="ad_from.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem prop="from_name" title="Name" :width="200"></TableItem>
          <TableItem prop="url" title="推广地址"></TableItem>
          <TableItem title="操作" align="center" :width="240">
            <template slot-scope="{ data }">
              <p-del-button permission="ad_from.destroy" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="ad_from.edit" text="编辑" @click="edit(data)"></p-button>
              <p-button glass="h-btn h-btn-s" permission="ad_from.number" text="数据" @click="goNumber(data)"></p-button>
            </template>
          </TableItem>
        </Table>
      </div>
      <div class="float-box mb-10">
        <Pagination align="right" v-model="pagination" @change="changePage" />
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
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      R.AdFrom.List(this.pagination).then(resp => {
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
            modal.close();
            this.getData(true);
          }
        }
      });
    },
    remove(data, item) {
      R.AdFrom.Delete({ id: item.id }).then(resp => {
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
            modal.close();
            this.getData();
          }
        }
      });
    },
    goNumber(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./statistics'], resolve);
          },
          datas: {
            id: item.id
          }
        }
      });
    }
  }
};
</script>
