<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">文章</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="12">
              <FormItem label="章节">
                <Select v-model="pagination.chapter_id" :filterable="true" :datas="chapters" keyName="id" titleName="name"></Select>
              </FormItem>
            </Cell>
            <Cell :width="12">
              <FormItem>
                <Button color="primary" @click="getData(true)">搜索</Button>
                <Button class="h-btn" @click="reset()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" icon="h-icon-plus" permission="addons.meedu_books.book.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="120"></TableItem>
          <TableItem title="章节" :width="150">
            <template slot-scope="{ data }">
              <span v-if="data.chapter">{{ data.chapter.name }}</span>
              <span class="c-red" v-else>已删除</span>
            </template>
          </TableItem>
          <TableItem prop="title" title="标题"></TableItem>
          <TableItem prop="view_times" title="浏览" unit="次" :width="100"></TableItem>
          <TableItem title="显示" :width="60">
            <template slot-scope="{ data }">
              <span v-if="data.is_show === 1">是</span>
              <span v-else class="red">否</span>
            </template>
          </TableItem>
          <TableItem title="试读" :width="60">
            <template slot-scope="{ data }">
              <span v-if="data.charge === 0" class="red">是</span>
              <span v-else>否</span>
            </template>
          </TableItem>
          <TableItem prop="published_at" title="上架时间" :width="200"></TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.meedu_books.book.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.meedu_books.book.update" text="编辑" @click="edit(data)"></p-button>

              <p-button
                glass="h-btn h-btn-primary h-btn-s"
                permission="addons.meedu_books.book_article.comments.list"
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
  props: ['bid'],
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0,
        book_id: this.bid,
        chapter_id: null
      },
      datas: [],
      chapters: [],
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.pagination.chapter_id = null;
      this.getData(true);
    },
    changePage(pagination) {
      this.pagination.page = parseInt(pagination.cur);
      this.pagination.size = parseInt(pagination.size);
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      R.Extentions.meeduBooks.Article.List(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.chapters = resp.data.chapters;
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
          },
          datas: {
            bid: this.bid
          }
        },
        events: {
          success: (modal, data) => {
            R.Extentions.meeduBooks.Article.Store(data).then(resp => {
              modal.close();
              HeyUI.$Message.success('成功');
              this.getData(true);
            });
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.meeduBooks.Article.Delete({ id: item.id }).then(resp => {
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
            R.Extentions.meeduBooks.Article.Update(data).then(resp => {
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
            require(['../article_comment/index'], resolve);
          },
          datas: {
            id: item.id
          }
        }
      });
    }
  }
};
</script>
