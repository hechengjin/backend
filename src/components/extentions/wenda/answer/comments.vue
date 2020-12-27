<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">评论</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Table ref="table" :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID" :width="80"></TableItem>
        <TableItem prop="user_id" title="用户ID" :width="80"></TableItem>
        <TableItem title="用户" :width="120">
          <template slot-scope="{ data }">
            <span>{{ data.user.nick_name }}</span>
          </template>
        </TableItem>
        <TableItem title="内容">
          <template slot-scope="{ data }">
            <div v-html="data.render_content"></div>
          </template>
        </TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.Wenda.question.answers.comments.delete" @click="remove(data)"></p-del-button>
          </template>
        </TableItem>
      </Table>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
      this.loading = true;
      R.Extentions.wenda.Question.AnswerComments(this.id).then(resp => {
        this.datas = resp.data.comments;
        this.loading = false;
      });
    },
    remove(item) {
      R.Extentions.wenda.Question.AnswerCommentDelete(item.id).then(() => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    }
  }
};
</script>
