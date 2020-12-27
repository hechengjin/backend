<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">优惠码</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="搜索">
                <input type="text" v-model="filter.key" placeholder="支持优惠码模糊搜索" />
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="UID">
                <user-filter v-model="filter.user_id"></user-filter>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData(true)">搜索</Button>
                <Button @click="reset">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
        <div class="alert">优惠码的 <b>U</b> 前缀是用户专属邀请码预留的，请勿在自定义优惠码中使用！</div>
      </div>
      <div class="float-box mb-10">
        <p-del-button text="批量删除" permission="promoCode.destroy.multi" @click="deleteSubmit()"></p-del-button>
        <p-button glass="h-btn h-btn-primary h-btn-s" icon="h-icon-plus" permission="promoCode.store" text="添加" @click="create()"></p-button>

        <p-button glass="h-btn h-btn-primary h-btn-s" permission="promoCode.import" text="批量导入" @click="showImportPage()"></p-button>

        <p-button glass="h-btn h-btn-primary h-btn-s" permission="promoCode.generator" text="批量生成" @click="showGeneratePage()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas" :checkbox="true" ref="table" @sort="sortEvt">
          <TableItem prop="id" :sort="true" title="ID" :width="80"></TableItem>
          <TableItem title="优惠码">
            <template slot-scope="{ data }">
              <copytext :copytext="data.code" />
            </template>
          </TableItem>
          <TableItem prop="invited_user_reward" :sort="true" title="抵扣" unit="元" :width="80"></TableItem>
          <TableItem prop="invite_user_reward" :sort="true" title="奖励" unit="元" :width="80"></TableItem>
          <TableItem title="可使用" :width="100">
            <template slot-scope="{ data }">
              <span v-if="data.use_times === 0 || data.use_times === null" class="red">不限制</span>
              <span v-else>{{ data.use_times }}次</span>
            </template>
          </TableItem>
          <TableItem prop="used_times" :sort="true" title="已使用" unit="次" :width="100"></TableItem>
          <TableItem title="创建时间" :width="120">
            <template slot-scope="{ data }">
              <date-text :date="data.created_at"></date-text>
            </template>
          </TableItem>
          <TableItem title="过期时间" :width="120">
            <template slot-scope="{ data }">
              <date-text :date="data.expired_at"></date-text>
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
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      datas: [],
      loading: false,
      filter: {
        key: null,
        user_id: null,
        sort: 'id',
        order: 'desc'
      }
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.filter.key = null;
      this.filter.user_id = null;
      this.filter.sort = 'id';
      this.filter.order = 'desc';
      this.getData(true);
    },
    sortEvt(sort) {
      this.filter.sort = sort.prop;
      this.filter.order = sort.type;
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
      R.PromoCode.List(data).then(resp => {
        this.datas = resp.data.data;
        this.pagination.total = resp.data.total;
        this.pagination.page = resp.data.current_page;
        this.pagination.size = resp.data.per_page;
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
    deleteSubmit() {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要删除的数据');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.PromoCode.Delete({ ids: ids }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    showImportPage() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./import'], resolve);
          }
        }
      });
    },
    showGeneratePage() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./generator'], resolve);
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData(true);
          }
        }
      });
    }
  }
};
</script>
