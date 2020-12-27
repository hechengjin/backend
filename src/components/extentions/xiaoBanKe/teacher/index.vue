<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">讲师</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          icon="h-icon-plus"
          permission="addons.XiaoBanKe.teacher.store"
          text="添加讲师"
          @click="create()"
        ></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="120"></TableItem>
          <TableItem prop="name" title="姓名"></TableItem>
          <TableItem prop="username" title="用户名" :width="200"></TableItem>
          <TableItem prop="password" title="密码" :width="200"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.XiaoBanKe.teacher.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.XiaoBanKe.teacher.edit" text="编辑" @click="edit(data)"></p-button>
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
      datas: [],
      pagination: {
        page: 1,
        size: 20,
        total: 0,
        keywords: ''
      },
      loading: false
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    changePage() {
      this.getData();
    },
    getData() {
      this.loading = true;
      R.Extentions.xiaoBanKe.Teacher.List(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
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
            HeyUI.$Message.success('成功');
            this.getData(true);
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.xiaoBanKe.Teacher.Delete({ id: item.id }).then(resp => {
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
            HeyUI.$Message.success('成功');
            this.getData();
          }
        }
      });
    }
  }
};
</script>
