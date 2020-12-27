<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">试题</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="分类">
                <Select v-model="filter.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="类型">
                <Select v-model="filter.type" :datas="types" keyName="id" titleName="name" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="难度">
                <Select v-model="filter.level" :datas="levels" keyName="id" titleName="name" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData()">过滤</Button>
                <Button @click="resetFilter()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary h-btn-s"
          permission="addons.Paper.question_category.list"
          text="试题分类"
          @click="showCategoriesPage()"
        ></p-button>

        <p-button glass="h-btn h-btn-primary h-btn-s" permission="addons.Paper.question.store" text="添加" @click="create()"></p-button>

        <p-button glass="h-btn h-btn-primary h-btn-s" permission="addons.Paper.question.import.csv" text="批量导入" @click="importFile()"></p-button>

        <p-del-button permission="addons.Paper.question.delete" text="批量删除" @click="deleteSubmit"></p-del-button>
      </div>

      <div class="float-box mb-10">
        <Table :loading="loading" :checkbox="true" :datas="datas" ref="table">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem title="分类" :width="80">
            <template slot-scope="{ data }">
              <span v-if="data.category">{{ data.category.name }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem prop="type_text" title="类型" :width="80"></TableItem>
          <TableItem prop="level_text" title="难度" :width="80"></TableItem>
          <TableItem prop="score" title="分数" unit="分" :width="80"></TableItem>
          <TableItem title="问题">
            <template slot-scope="{ data }">
              <div v-html="data.content"></div>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="100">
            <template slot-scope="{ data }">
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.Paper.question.update" text="编辑" @click="edit(data)"></p-button>
            </template>
          </TableItem>
        </Table>
      </div>

      <div class="float-box mb-10">
        <Pagination class="mt-10" v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
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
      loading: false,
      levels: [],
      types: [],
      categories: [],
      downloadUrl: '',
      filter: {
        level: null,
        type: null,
        category_id: null
      }
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    resetFilter() {
      this.filter.level = null;
      this.filter.type = null;
      this.filter.category_id = null;
      this.getData();
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
      Object.assign(data, this.filter);
      R.Extentions.paper.Question.List(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
        this.levels = resp.data.levels;
        this.types = resp.data.types;
        this.categories = resp.data.categories;
        this.downloadUrl = resp.data.download_url;
      });
    },
    deleteSubmit() {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要删除的试题');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.paper.Question.Delete({ ids: ids }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
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
            this.getData();
            modal.close();
          }
        }
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
            this.getData();
            modal.close();
          }
        }
      });
    },
    importFile() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./import'], resolve);
          },
          datas: {
            url: this.downloadUrl
          }
        },
        events: {
          success: (modal, data) => {
            this.getData();
            modal.close();
          }
        }
      });
    },
    showCategoriesPage() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['../question_category/index'], resolve);
          }
        }
      });
    }
  }
};
</script>
