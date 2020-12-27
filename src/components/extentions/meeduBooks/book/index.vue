<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">电子书</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="搜索">
                <input type="text" v-model="pagination.key" placeholder="书名搜索" />
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="分类">
                <template v-slot:label>分类</template>
                <Select v-model="pagination.cid" :filterable="true" :datas="categories" keyName="id" titleName="name"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData(true)">搜索</Button>
                <Button class="h-btn" @click="reset()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary"
          permission="addons.meedu_books.book_category.list"
          text="电子书分类"
          @click="showCategoriesPage()"
        ></p-button>
        <p-button glass="h-btn h-btn-primary" icon="h-icon-plus" permission="addons.meedu_books.book.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem title="分类" :width="120">
            <template slot-scope="{ data }">
              <span v-if="data.category">{{ data.category.name }}</span>
              <span class="c-red" v-else>已删除</span>
            </template>
          </TableItem>
          <TableItem prop="name" title="名字"></TableItem>
          <TableItem prop="charge" title="价格" unit="元" :width="80"></TableItem>
          <TableItem prop="view_times" title="浏览" unit="次" :width="120"></TableItem>
          <TableItem prop="user_count" title="订阅" unit="人" :width="120"></TableItem>
          <TableItem prop="published_at" title="上架" :width="120"></TableItem>
          <TableItem title="操作" align="center" :width="300">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.meedu_books.book.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.meedu_books.book.update" text="编辑" @click="edit(data)"></p-button>

              <p-button
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.meedu_books.book_chapter.list"
                text="章节"
                @click="goChapter(data)"
              ></p-button>

              <p-button
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.meedu_books.book_article.list"
                text="文章"
                @click="showArticlesPage(data)"
              ></p-button>

              <p-button
                glass="h-btn h-btn-primary h-btn-s"
                permission="addons.meedu_books.book.comments.list"
                text="评论"
                @click="showCommentsPage(data)"
              ></p-button>
            </template>
          </TableItem>
        </Table>
      </div>

      <div class="float-box mb-10">
        <Pagination class="mt-10" align="right" :size="pagination.size" :cur="pagination.page" :total="pagination.total" @change="changePage" />
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
        total: 0,
        key: null,
        cid: null
      },
      datas: [],
      categories: [],
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.pagination.key = null;
      this.pagination.cid = null;
      this.getData(true);
    },
    changePage(pagination) {
      this.pagination.page = pagination.cur;
      this.pagination.size = pagination.size;
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      R.Extentions.meeduBooks.Book.List(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.categories = resp.data.categories;
        this.loading = false;
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
            R.Extentions.meeduBooks.Book.Store(data).then(() => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.meeduBooks.Book.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
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
            R.Extentions.meeduBooks.Book.Update(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    goChapter(item) {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['../chapter/index'], resolve);
          },
          datas: {
            bid: item.id
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
            require(['../book_comment/index'], resolve);
          },
          datas: {
            bid: item.id
          }
        }
      });
    },
    showArticlesPage(item) {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['../article/index'], resolve);
          },
          datas: {
            bid: item.id
          }
        }
      });
    }
  }
};
</script>
