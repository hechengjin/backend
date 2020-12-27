<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">角色</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" permission="administrator_role.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem prop="display_name" title="角色名" :width="150"></TableItem>
          <TableItem prop="slug" title="Slug" :width="120"></TableItem>
          <TableItem prop="description" title="描述"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="administrator_role.destroy" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="administrator_role.edit" text="编辑" @click="edit(data)"></p-button>
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
      R.AdministratorRole.List(this.pagination).then(resp => {
        this.datas = resp.data.data;
        this.pagination.total = resp.data.total;
        this.loading = false;
      });
    },
    create() {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['./create'], resolve);
          }
        },
        events: {
          success: (modal, data) => {
            R.AdministratorRole.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.AdministratorRole.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    edit(item) {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
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
            R.AdministratorRole.Update(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData();
            });
          }
        }
      });
    }
  }
};
</script>
