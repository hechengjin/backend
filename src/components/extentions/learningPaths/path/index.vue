<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">学习路径</span>
    </div>
    <div class="h-panel-body">
      <div class="mb-10">
        <p-button glass="h-btn h-btn-primary" icon="h-icon-plus" permission="addons.learnPaths.path.store" text="添加" @click="create()"></p-button>
      </div>
      <Table :loading="loading" :datas="datas">
        <TableItem prop="name" title="路径名"></TableItem>
        <TableItem title="价格" :width="150">
          <template slot-scope="{ data }">
            <span>￥{{ data.charge }}</span> /
            <span style="text-decoration: line-through">￥{{ data.original_charge }}</span>
          </template>
        </TableItem>
        <TableItem title="显示" :width="100">
          <template slot-scope="{ data }">
            <span>{{ data.is_show === 1 ? '是' : '否' }}</span>
          </template>
        </TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.learnPaths.path.delete" @click="remove(datas, data)"></p-del-button>
            <p-button glass="h-btn h-btn-primary h-btn-s" permission="addons.learnPaths.path.update" text="编辑" @click="edit(data)"></p-button>
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
        size: 20,
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
      R.Extentions.learningPaths.Paths.List(this.pagination).then(resp => {
        this.datas = resp.data.data;
        this.pagination.total = resp.data.total;
        this.pagination.page = resp.data.current_page;
        this.pagination.size = resp.data.per_page;
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
            R.Extentions.learningPaths.Paths.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.learningPaths.Paths.Delete({ id: item.id }).then(resp => {
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
            R.Extentions.learningPaths.Paths.Update(data).then(resp => {
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
