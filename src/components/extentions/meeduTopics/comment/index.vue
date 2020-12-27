<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">评论</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
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
              <FormItem>
                <Button color="primary" @click="getData(true)">过滤</Button>
                <Button @click="reset()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <p-del-button permission="addons.meedu_topics.topic.comment.check" text="批量审核通过" @click="multiCheck(1)"></p-del-button>
        <p-del-button permission="addons.meedu_topics.topic.comment.check" text="批量审核拒绝" @click="multiCheck(0)"></p-del-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas" :checkbox="true" ref="table">
          <TableItem prop="id" title="ID" :width="80"></TableItem>
          <TableItem title="用户">
            <template slot-scope="{ data }">
              <span v-if="data.user">{{ data.user.nick_name }}</span>
              <span v-else class="red">已删除</span>
            </template>
          </TableItem>
          <TableItem title="评论内容">
            <template slot-scope="{ data }">
              <div v-html="data.content"></div>
            </template>
          </TableItem>
          <TableItem prop="created_at" title="时间"></TableItem>
          <TableItem title="状态">
            <template slot-scope="{ data }">
              <span v-if="data.is_check === 1">通过</span>
              <span v-else class="red">拒绝</span>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="100">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.meedu_topics.topic.comment.delete" @click="remove(datas, data)"></p-del-button>
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
  props: ['id'],
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      filter: {
        user_id: null
      },
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    reset() {
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
      data.topic_id = this.id;
      data.user_id = this.filter.user_id;
      R.Extentions.meeduTopics.Comment.Index(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
      });
    },
    remove(data, item) {
      R.Extentions.meeduTopics.Comment.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    multiCheck(status) {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要操作的数据');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.meeduTopics.Comment.Check({ ids: ids, is_check: status }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    }
  }
};
</script>
