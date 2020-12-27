<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">话题</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="UID">
                <user-filter v-model="filter.user_id"></user-filter>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="分类">
                <Select v-model="filter.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData(true)">过滤</Button>
                <Button @click="reset()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" permission="addons.meedu_topics.category.list" text="分类" @click="showCategoriesPage()"></p-button>

        <p-button
          glass="h-btn h-btn-primary"
          icon="h-icon-plus"
          permission="addons.meedu_topics.topic.store"
          text="添加"
          @click="create()"
        ></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem title="分类" :width="100">
            <template slot-scope="{ data }">
              <span v-if="data.category">{{ data.category.name }}</span>
              <span class="red" v-else>已删除</span>
            </template>
          </TableItem>
          <TableItem title="用户" :width="100">
            <template slot-scope="{ data }">
              <span v-if="data.user">{{ data.user.nick_name }}</span>
              <span class="red" v-else>系统</span>
            </template>
          </TableItem>
          <TableItem prop="title" title="标题"></TableItem>
          <TableItem prop="charge" title="价格" :width="80"></TableItem>
          <TableItem prop="view_times" title="浏览" :width="80"></TableItem>
          <TableItem prop="users_count" title="付费" :width="80"></TableItem>
          <TableItem prop="comments_count" title="评论" :width="80"></TableItem>
          <TableItem prop="vote_count" title="点赞" :width="80"></TableItem>
          <TableItem title="操作" align="center" :width="300">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.meedu_topics.topic.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.meedu_topics.topic.update" text="编辑" @click="edit(data)"></p-button>

              <p-button
                glass="h-btn h-btn-primary h-btn-s"
                permission="addons.meedu_topics.comments"
                text="评论"
                @click="showCommentsPage(data)"
              ></p-button>

              <p-button
                glass="h-btn h-btn-primary h-btn-s"
                permission="addons.meedu_topics.orders"
                text="订单"
                @click="showOrdersPage(data)"
              ></p-button>
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
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      filter: {
        category_id: null,
        user_id: null
      },
      categories: [],
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.filter.category_id = null;
      this.filter.user_id = null;
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
      let data = this.pagination;
      data.category_id = this.filter.category_id;
      data.user_id = this.filter.user_id;
      R.Extentions.meeduTopics.Topic.List(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.categories = resp.data.categories;
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
            R.Extentions.meeduTopics.Topic.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.meeduTopics.Topic.Delete({ id: item.id }).then(resp => {
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
            R.Extentions.meeduTopics.Topic.Update(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    showCommentsPage(item) {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['../comment/index'], resolve);
          },
          datas: {
            id: item.id
          }
        }
      });
    },
    showOrdersPage(item) {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['./user'], resolve);
          },
          datas: {
            id: item.id
          }
        }
      });
    },
    showCategoriesPage() {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['../category/index'], resolve);
          }
        }
      });
    }
  }
};
</script>
