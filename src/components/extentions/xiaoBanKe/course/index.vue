<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">课程</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          permission="addons.XiaoBanKe.course_category.list"
          text="课程分类"
          @click="showCategoryPage()"
        ></p-button>
        <p-button glass="h-btn h-btn-primary" permission="addons.XiaoBanKe.teacher.list" text="讲师管理" @click="showTeacherPage()"></p-button>
        <p-button glass="h-btn h-btn-primary" permission="addons.XiaoBanKe.order.list" text="订单列表" @click="showOrderPage()"></p-button>
        <p-button
          glass="h-btn h-btn-primary"
          icon="h-icon-plus"
          permission="addons.XiaoBanKe.course.store"
          text="添加课程"
          @click="create()"
        ></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="70"></TableItem>
          <TableItem prop="type_text" title="类型" :width="70"></TableItem>
          <TableItem title="分类" :width="80">
            <template slot-scope="{ data }">
              <span v-if="data.category">{{ data.category.name }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem prop="title" title="课程名"></TableItem>
          <TableItem title="价格" unit="元">
            <template slot-scope="{ data }">
              <span>￥{{ data.charge }}</span> /
              <span style="text-decoration: line-through">￥{{ data.original_charge }}</span>
            </template>
          </TableItem>
          <TableItem title="已报名/上限">
            <template slot-scope="{ data }">
              <span>{{ data.join_people_num || 0 }}</span
              >/
              <span>{{ data.max_people_num }}</span>
            </template>
          </TableItem>
          <TableItem prop="start_at" title="开课时间"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.XiaoBanKe.course.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.XiaoBanKe.course.edit" text="编辑" @click="edit(data)"></p-button>
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
    this.init();
  },
  methods: {
    init() {
      this.getData();
    },
    changePage() {
      this.getData();
    },
    getData() {
      this.loading = true;
      R.Extentions.xiaoBanKe.Course.List(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.pagination.size = resp.data.data.per_page;
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
      R.Extentions.xiaoBanKe.Course.Delete({ id: item.id }).then(resp => {
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
    },

    showCategoryPage() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['../course_category/index'], resolve);
          }
        }
      });
    },
    showTeacherPage() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['../teacher/index'], resolve);
          }
        }
      });
    },
    showOrderPage() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['../order/index'], resolve);
          }
        }
      });
    }
  }
};
</script>
