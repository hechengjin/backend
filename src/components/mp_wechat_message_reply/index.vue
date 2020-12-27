<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">微信公众号消息回复</span>
    </div>
    <div class="h-panel-body">
      <div class="mb-10">
        <p-button glass="h-btn h-btn-primary" permission="mpWechatMessageReply.store" text="添加规则" @click="create()"></p-button>
      </div>
      <Table :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID" :width="120"></TableItem>
        <TableItem prop="type" title="类型" :width="120"></TableItem>
        <TableItem title="内容" :width="200">
          <template slot-scope="{ data }">
            <span v-if="data.type === 'text'">{{ data.rule }}</span>
            <span v-else-if="data.type === 'event'">{{ data.event_type }}:{{ data.event_key }}</span>
          </template>
        </TableItem>
        <TableItem title="回复内容">
          <template slot-scope="{ data }">
            <div v-html="data.reply_content"></div>
          </template>
        </TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="mpWechatMessageReply.destroy" @click="remove(datas, data)"></p-del-button>
            <p-button glass="h-btn h-btn-s h-btn-primary" permission="mpWechatMessageReply.edit" text="编辑" @click="edit(data)"></p-button>
          </template>
        </TableItem>
      </Table>
      <div class="mt-10">
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
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      R.MpWechatMessageReply.List(data).then(resp => {
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
            this.getData(true);
          }
        }
      });
    },
    remove(data, item) {
      R.MpWechatMessageReply.Delete({ id: item.id }).then(resp => {
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
            this.getData();
          }
        }
      });
    }
  }
};
</script>
