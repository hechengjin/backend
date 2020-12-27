<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">邀请余额提现</span>
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
              <FormItem label="会员">
                <Select v-model="filter.status" :datas="statusMap" keyName="id" titleName="name"></Select>
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
        <p-del-button permission="member.inviteBalance.withdrawOrders" text="批量操作" @click="multiHandle"></p-del-button>
      </div>

      <div class="float-box mb-10">
        <Table ref="table" :loading="loading" :datas="datas" :checkbox="true">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem prop="user_id" title="用户ID" :width="80"></TableItem>
          <TableItem title="用户" :width="150">
            <template slot-scope="{ data }">
              <span v-if="users[data.user_id]">{{ users[data.user_id].nick_name }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem prop="total" title="金额" unit="元" :width="100"></TableItem>
          <TableItem title="渠道" :width="150">
            <template slot-scope="{ data }">
              <copytext :copytext="data.channel" />
            </template>
          </TableItem>
          <TableItem title="渠道姓名" :width="150">
            <template slot-scope="{ data }">
              <copytext :copytext="data.channel_name" />
            </template>
          </TableItem>
          <TableItem title="渠道账号" :width="200">
            <template slot-scope="{ data }">
              <copytext :copytext="data.channel_account" />
            </template>
          </TableItem>
          <TableItem title="状态" :wdith="100">
            <template slot-scope="{ data }">
              <span v-if="data.status_text === '成功'" class="red">{{ data.status_text }}</span>
              <span v-else>{{ data.status_text }}</span>
            </template>
          </TableItem>
          <TableItem title="创建时间" :width="120">
            <template slot-scope="{ data }">
              <date-text :date="data.created_at"></date-text>
            </template>
          </TableItem>
          <TableItem prop="remark" title="备注"></TableItem>
        </Table>
      </div>

      <div class="float-box mb-10">
        <Pagination v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
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
        keywords: ''
      },
      datas: [],
      users: [],
      loading: false,
      filter: {
        user_id: null,
        status: -1
      },
      statusMap: [
        {
          id: -1,
          name: '全部'
        },
        {
          id: 0,
          name: '已提交'
        },
        {
          id: 1,
          name: '成功'
        },
        {
          id: 2,
          name: '失败'
        }
      ]
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
      this.filter.user_id = null;
      this.filter.status = -1;
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
      Object.assign(data, this.filter);
      R.Member.InviteBalanceWithdrawOrders(data).then(resp => {
        this.datas = resp.data.orders.data;
        for (let i = 0; i < this.datas.length; i++) {
          this.datas[i]._disabledSelect = this.datas[i].status !== 0;
        }
        this.users = resp.data.users;
        this.pagination.total = resp.data.orders.total;
        this.loading = false;
      });
    },
    multiHandle() {
      let selectedRows = this.$refs.table.getSelection();
      if (selectedRows.length == 0) {
        HeyUI.$Message.warn('请选择需要操作的数据');
        return;
      }
      let ids = [];
      for (var i = 0; i < selectedRows.length; i++) {
        ids.push(selectedRows[i].id);
      }

      this.$Modal({
        component: {
          vue: resolve => {
            require(['./children/InviteBalanceHandle'], resolve);
          },
          datas: {
            ids: ids
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
