<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">回答</span>
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
        <TableItem prop="vote_count" title="点赞" unit="次" :width="80"></TableItem>
        <TableItem title="内容">
          <template slot-scope="{ data }">
            <div v-html="data.render_content"></div>
          </template>
        </TableItem>
        <TableItem title="答案" :width="50">
          <template slot-scope="{ data }">
            <span v-if="data.is_correct === 1" class="red">是</span>
            <span v-else>否</span>
          </template>
        </TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button permission="addons.Wenda.question.answers.delete" @click="remove(data)"></p-del-button>
            <p-del-button
              v-if="solved === false"
              permission="addons.Wenda.question.answers.setTrue"
              text="设为答案"
              @click="setTrue(data)"
            ></p-del-button>

            <p-button
              glass="h-btn h-btn-s h-btn-primary"
              permission="addons.Wenda.question.answers.comments"
              text="评论"
              @click="showCommentsPage(data)"
            ></p-button>
          </template>
        </TableItem>
      </Table>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id', 'solved'],
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
      R.Extentions.wenda.Question.Answers(this.id).then(resp => {
        this.datas = resp.data.answers;
        this.loading = false;
      });
    },
    remove(item) {
      R.Extentions.wenda.Question.AnswerDelete(this.id, item.id).then(() => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    setTrue(item) {
      R.Extentions.wenda.Question.AnswerSelected(this.id, item.id).then(() => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    showCommentsPage(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./comments'], resolve);
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
