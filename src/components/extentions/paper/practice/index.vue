<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">练习</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="分类">
                <Select v-model="filer.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="搜索" prop="key">
                <input type="text" v-model="filer.key" placeholder="搜索" />
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData(true)">过滤</Button>
                <Button @click="resetFilter()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <p-button
          glass="h-btn h-btn-primary h-btn-s"
          permission="addons.Paper.paper_category.list"
          text="分类"
          @click="showCategoriesPage()"
        ></p-button>

        <p-button
          glass="h-btn h-btn-primary h-btn-s"
          icon="h-icon-plus"
          permission="addons.Paper.practice.store"
          text="添加"
          @click="create()"
        ></p-button>

        <p-del-button permission="addons.Paper.practice.delete" text="批量删除" @click="deleteSubmit()"></p-del-button>
      </div>

      <div class="float-box mb-10">
        <Table ref="table" :loading="loading" :datas="datas" :checkbox="true" @sort="sortEvt">
          <TableItem prop="id" title="ID" :width="80" :sort="true"></TableItem>
          <TableItem title="分类" :width="120">
            <template slot-scope="{ data }">
              <span v-if="data.category">{{ data.category.name }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem prop="name" title="练习名"></TableItem>
          <TableItem prop="question_count" title="题目数" unit="个"></TableItem>
          <TableItem title="VIP免费" align="center" :width="80">
            <template slot-scope="{ data }">
              <span>{{ data.is_vip_free === 1 ? '是' : '否' }}</span>
            </template>
          </TableItem>
          <TableItem title="价格" :width="120">
            <template slot-scope="{ data }">
              <span v-if="data.is_free === 1" class="red">免费</span>
              <span v-else>￥{{ data.charge }}</span>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.Paper.practice.update" text="编辑" @click="edit(data)"></p-button>
              <p-button
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.Paper.practice_chapter.list"
                text="章节"
                @click="showChaptersPage(data)"
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
      filer: {
        category_id: null,
        key: null,
        sort: null,
        order: null
      },
      datas: [],
      loading: false,
      categories: []
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    changePage() {
      this.getData();
    },
    sortEvt(sort) {
      this.filer.order = sort.prop;
      this.filer.sort = sort.type;
      this.getData(true);
    },
    resetFilter() {
      this.filer = {
        category_id: null,
        key: null,
        sort: null,
        order: null
      };
      this.getData(true);
    },
    getData(reset = true) {
      if (reset) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      Object.assign(data, this.filer);
      R.Extentions.paper.Practice.List(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
        this.categories = resp.data.categories;
      });
    },
    deleteSubmit() {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要删除的练习');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.paper.Practice.Delete({ ids: ids }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
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
            this.getData(false);
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
            require(['../paper_category/index'], resolve);
          }
        }
      });
    },
    showChaptersPage(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./chapter/index'], resolve);
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
