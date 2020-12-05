<style lang="less" scoped>
.nav-item {
  width: 100%;
  height: auto;
  float: left;
  border-bottom: 1px dashed rgba(0, 0, 0, 0.2);
  background-color: rgba(0, 0, 0, 0.02);

  &:hover {
    background-color: rgba(0, 0, 0, 0.04);
    cursor: pointer;
  }

  .body {
    width: 100%;
    height: auto;
    float: left;
    display: flex;
    padding-left: 15px;
    padding-right: 15px;
    line-height: 3rem;

    .name {
      flex: 1;
      font-size: 1rem;
      color: #333;
    }

    .option {
      width: 200px;
      text-align: right;
    }
  }

  .children {
    width: 100%;
    height: auto;
    float: left;
    padding-left: 30px;
    background-color: rgba(0, 0, 0, 0.05);

    .children-nav-item {
      width: 100%;
      height: auto;
      float: left;
      color: #333333;
      display: flex;
      padding-left: 15px;
      padding-right: 15px;
      line-height: 3rem;

      .name {
        flex: 1;
        font-size: 0.9rem;
      }

      .option {
        width: 200px;
        text-align: right;
      }
    }
  }
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">首页导航</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" icon="h-icon-plus" permission="nav.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <div class="nav-item" v-for="nav in datas" :key="nav.id">
          <div class="body">
            <div class="name">{{ nav.platform }} - {{ nav.name }}</div>
            <div class="option">
              <p-del-button permission="nav.destroy" @click="remove(nav)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="nav.edit" text="编辑" @click="edit(nav)"></p-button>
            </div>
          </div>
          <div class="children">
            <div class="children-nav-item" v-for="childrenNav in nav.children" :key="childrenNav.id">
              <div class="name">{{ childrenNav.name }}</div>
              <div class="option">
                <p-del-button permission="nav.destroy" @click="remove(childrenNav)"></p-del-button>
                <p-button glass="h-btn h-btn-s h-btn-primary" permission="nav.edit" text="编辑" @click="edit(childrenNav)"></p-button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="float-box mb-10">
        <Pagination v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
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
      R.Nav.List(this.pagination).then(resp => {
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
            modal.close();
            this.getData();
          }
        }
      });
    },
    remove(item) {
      R.Nav.Delete({ id: item.id }).then(resp => {
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
