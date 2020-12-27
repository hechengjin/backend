<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">试卷</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row>
            <Cell :width="6">
              <FormItem label="分类" prop="category_id">
                <Select v-model="pagination.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
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

      <div class="mb-10">
        <p-button
          glass="h-btn h-btn-s h-btn-primary"
          icon="h-icon-plus"
          permission="addons.Paper.paper.store"
          text="添加"
          @click="create()"
        ></p-button>

        <p-button
          glass="h-btn h-btn-s h-btn-primary"
          permission="addons.Paper.paper_category.list"
          text="分类"
          @click="showCategoriesPage()"
        ></p-button>
      </div>

      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem prop="id" title="ID" :width="60"></TableItem>
          <TableItem title="分类" :width="80">
            <template slot-scope="{ data }">
              <span v-if="data.category">{{ data.category.name }}</span>
              <span class="red" v-else>已删除</span>
            </template>
          </TableItem>
          <TableItem prop="title" title="标题"></TableItem>
          <TableItem title="分数/及格" :width="120">
            <template slot-scope="{ data }">
              <span>{{ data.score }}分/{{ data.pass_score }}分</span>
            </template>
          </TableItem>
          <TableItem prop="expired_minutes" title="时长" unit="分钟" :width="80"></TableItem>
          <TableItem title="试题" :width="80">
            <template slot-scope="{ data }">
              <template v-if="data.is_random !== 1">
                <p-button
                  glass="h-btn h-btn-s h-btn-primary"
                  permission="addons.Paper.paper.questions.list"
                  text="试题"
                  @click="showQuestion(data)"
                ></p-button>
              </template>
              <span v-else>随机</span>
            </template>
          </TableItem>
          <TableItem title="参与规则" :width="80">
            <template slot-scope="{ data }">
              <span class="blue" v-if="data.enabled_invite === 1">仅邀请</span>
              <span class="red" v-else-if="data.is_free === 1">免费</span>
              <span v-else-if="data.charge > 0">￥{{ data.charge }}</span>
              <span v-else>{{ data.is_vip_free ? '会员免费' : '' }} {{ data.required_courses.length > 0 ? '购买课程' : '' }}</span>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="300">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.Paper.paper.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.Paper.paper.update" text="编辑" @click="edit(data)"></p-button>

              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.Paper.paper.users" text="用户" @click="showUsers(data)"></p-button>

              <p-button
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.Paper.paper.userPaper"
                text="考试记录"
                @click="showUserPapers(data)"
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
        total: 0,
        category_id: null
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
    resetFilter() {
      this.pagination.category_id = null;
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
      R.Extentions.paper.Paper.List(this.pagination).then(resp => {
        this.datas = resp.data.data.data;
        this.categories = resp.data.categories;
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
            require(['../paper_category/index'], resolve);
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.paper.Paper.Delete({ id: item.id }).then(resp => {
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
            this.getData();
            modal.close();
          }
        }
      });
    },
    showUsers(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./user'], resolve);
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
    showUserPapers(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./user_papers'], resolve);
          },
          datas: {
            paper_id: item.id
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
    showQuestion(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./question'], resolve);
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
    }
  }
};
</script>
