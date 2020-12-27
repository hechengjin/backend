<style lang="less" scoped>
</style>
<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <p-del-button permission="addons.Paper.practice_chapter.questions.store" text="添加" @click="addQuestion()"></p-del-button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="12">
              <FormItem label="分类">
                <Select v-model="filter.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
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
        <Table ref="table" :loading="loading" :checkbox="true" :datas="datas">
          <TableItem title="ID" :width="80">
            <template slot-scope="{ data }">
              {{ data.id }}
            </template>
          </TableItem>
          <TableItem title="分类" :width="80">
            <template slot-scope="{ data }">
              {{ data.category.name }}
            </template>
          </TableItem>
          <TableItem title="类型" :width="120">
            <template slot-scope="{ data }">
              {{ data.type_text }}
            </template>
          </TableItem>
          <TableItem title="难度">
            <template slot-scope="{ data }">
              {{ data.level_text }}
            </template>
          </TableItem>
          <TableItem title="内容">
            <template slot-scope="{ data }">
              <div v-html="data.content"></div>
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
  props: ['id'],
  data() {
    return {
      pagination: {
        page: 1,
        size: 20,
        total: 0
      },
      filter: {
        category_id: null,
        id: this.id
      },
      questions: [],
      datas: [],
      categories: [],
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
    resetFilter() {
      this.filter.category_id = null;
      this.id = null;
      this.getData(true);
    },
    getData(reset = false) {
      this.loading = true;
      if (reset) {
        this.pagination.page = 1;
      }
      let data = this.pagination;
      Object.assign(data, this.filter);
      R.Extentions.paper.PracticeChapter.QuestionsCreate(data).then(resp => {
        this.loading = false;

        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.questions = resp.data.questions;
        this.categories = resp.data.categories;
      });
    },
    addQuestion(quesiton) {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要移除的试题');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.paper.PracticeChapter.QuestionsStore({ id: this.id, qids: ids }).then(() => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    }
  }
};
</script>
