<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">课程分类</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="mb-10">
        <p-button glass="h-btn h-btn-primary" permission="addons.XiaoBanKe.course_category.store" text="添加分类" @click="create()"></p-button>
      </div>
      <Table ref="table" :loading="loading" :datas="datas">
        <TableItem prop="name" title="分类名" treeOpener></TableItem>
        <TableItem prop="sort" title="升序"></TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.XiaoBanKe.course_category.delete" @click="remove(datas, data)"></p-del-button>
            <p-button
              glass="h-btn h-btn-s h-btn-primary"
              permission="addons.XiaoBanKe.course_category.edit"
              text="编辑"
              @click="edit(data)"
            ></p-button>
          </template>
        </TableItem>
      </Table>
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
    this.getData();
  },
  methods: {
    changePage() {
      this.getData();
    },
    getData() {
      this.loading = true;
      R.Extentions.xiaoBanKe.CourseCategory.List(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.loading = false;
        setTimeout(() => {
          this.$refs.table.expandAll();
        }, 500);
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
      R.Extentions.xiaoBanKe.CourseCategory.Delete({ id: item.id }).then(resp => {
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
            this.getData(true);
          }
        }
      });
    }
  }
};
</script>
