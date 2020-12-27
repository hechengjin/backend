<style lang="less" scoped>
.role-item {
  width: 300px;
  height: auto;
  float: left;
  background-color: rgba(0, 0, 0, 0.05);
  padding: 15px 30px;
  border-radius: 20px;
  margin-right: 30px;
  margin-bottom: 30px;

  .options {
    width: 100%;
    height: auto;
    float: left;
    text-align: right;
    margin-top: 30px;
  }

  .name {
    width: 100%;
    height: auto;
    float: left;
    font-size: 24px;
    font-weight: 700;
    color: #333333;
    text-align: center;
    margin-bottom: 50px;
    margin-top: 30px;
  }

  .days {
    width: 100%;
    height: auto;
    float: left;
    font-size: 18px;
    color: #333333;
    text-align: center;
    margin-bottom: 50px;
  }

  .charge {
    width: 100%;
    height: auto;
    float: left;
    font-size: 30px;
    color: red;
    text-align: center;
  }

  &:hover {
    cursor: pointer;
    background-color: rgba(0, 0, 0, 0.1);
  }
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">VIP会员</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" permission="role.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <div class="role-item" v-for="role in datas" :key="role.id">
          <div class="name">{{ role.name }}</div>
          <div class="days">{{ role.expire_days }}天</div>
          <div class="charge">￥{{ role.charge }}</div>
          <div class="options">
            <p-del-button permission="role.destroy" @click="remove(datas, role)"></p-del-button>
            <p-button glass="h-btn h-btn-s h-btn-primary" permission="role.edit" text="编辑" @click="edit(role)"></p-button>
          </div>
        </div>
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
      R.Role.List(this.pagination).then(resp => {
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
      R.Role.Delete({ id: item.id }).then(resp => {
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
    }
  }
};
</script>
