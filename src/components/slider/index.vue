<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">幻灯片</span>
    </div>
    <div class="h-panel-body">
      <div class="flaot-box mb-10">
        <p-button glass="h-btn h-btn-primary" permission="slider.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="flaot-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="platform" title="平台" :width="80"></TableItem>
          <TableItem prop="sort" title="排序" :width="80"></TableItem>
          <TableItem title="封面">
            <template slot-scope="{ data }">
              <img :src="data.thumb" width="120" height="60" />
            </template>
          </TableItem>
          <TableItem prop="url" title="URL"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="slider.destroy" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="slider.edit" text="编辑" @click="edit(data)"></p-button>
            </template>
          </TableItem>
        </Table>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
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
      this.loading = true;
      R.Slider.List(this.pagination).then(resp => {
        this.datas = resp.data;
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
            modal.close();
            this.getData(true);
          }
        }
      });
    },
    remove(data, item) {
      R.Slider.Delete({ id: item.id }).then(resp => {
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
            modal.close();
            this.getData();
          }
        }
      });
    }
  }
};
</script>
