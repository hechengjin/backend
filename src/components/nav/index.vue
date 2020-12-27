<style lang="less" scoped>
.nav-item {
  width: 100%;
  height: auto;
  float: left;

  &:hover {
    background-color: rgba(0, 0, 0, 0.02);
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

    .children-nav-item {
      width: 100%;
      height: auto;
      float: left;
      color: #333333;
      display: flex;
      padding-left: 15px;
      padding-right: 15px;
      line-height: 3rem;

      &:hover {
        background-color: rgba(0, 0, 0, 0.02);
      }

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

.nav-panel {
  width: 100%;
  height: auto;
  float: left;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 5px;

  .panel-title {
    width: 100%;
    height: auto;
    float: left;
    padding-left: 20px;
    line-height: 50px;
    font-size: 15px;
    color: #333;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    font-weight: 600;
  }
  .panel-body {
    width: 100%;
    height: auto;
    float: left;
    padding: 20px;
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
        <p-button glass="h-btn h-btn-primary" permission="nav.store" text="添加" @click="create()"></p-button>
      </div>

      <div class="float-box mb-10">
        <Row :space="10">
          <Cell :width="12">
            <div class="pc-box nav-panel">
              <div class="panel-title">PC导航</div>
              <div class="panel-body">
                <div class="nav-item" v-for="nav in pcList" :key="nav.id">
                  <div class="body">
                    <div class="name">{{ nav.name }}</div>
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
            </div>
          </Cell>
          <Cell :width="12">
            <div class="h5-box nav-panel">
              <div class="panel-title">H5导航</div>
              <div class="panel-body">
                <div class="nav-item" v-for="nav in h5List" :key="nav.id">
                  <div class="body">
                    <div class="name">{{ nav.name }}</div>
                    <div class="option">
                      <p-del-button permission="nav.destroy" @click="remove(nav)"></p-del-button>
                      <p-button glass="h-btn h-btn-s h-btn-primary" permission="nav.edit" text="编辑" @click="edit(nav)"></p-button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </Cell>
        </Row>
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
  computed: {
    pcList() {
      let list = [];
      for (let i = 0; i < this.datas.length; i++) {
        if (this.datas[i].platform === 'PC') {
          list.push(this.datas[i]);
        }
      }
      return list;
    },
    h5List() {
      let list = [];
      for (let i = 0; i < this.datas.length; i++) {
        if (this.datas[i].platform === 'h5') {
          list.push(this.datas[i]);
        }
      }
      return list;
    }
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    changePage() {
      this.getData();
    },
    getData() {
      this.loading = true;
      R.Nav.List().then(resp => {
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
