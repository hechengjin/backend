<style lang="less" scoped>
.banner {
  text-align: center;

  .title {
    width: 100%;
    height: auto;
    float: left;
    font-size: 18px;
    line-height: 40px;
  }

  .value {
    width: 100%;
    height: auto;
    float: left;
    font-size: 24px;
    font-weight: 600;
    color: #000;
    line-height: 64px;
  }
}

.paper-quesiton {
  width: 100%;
  height: 500px;
  float: left;
  overflow-y: auto;
  border: 1px solid #aaa;
  padding: 15px;

  .paper-item {
    width: 100%;
    height: auto;
    float: left;
    background-color: rgba(0, 0, 0, 0.02);
    padding: 15px;
    border-radius: 15px;
    margin-top: 10px;

    &:hover {
      cursor: pointer;
      background-color: rgba(0, 0, 0, 0.04);
    }

    .info {
      color: rgba(0, 0, 0, 0.4);
      font-size: 0.8rem;
      margin-bottom: 5px;
    }
    .content {
      color: #333;
    }
  }
}

.question-box {
  position: relative;
  width: 100%;
  height: 500px;
  float: left;

  .filter-box {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    width: 100%;
    height: 60px;
    padding: 10px 15px;
    background-color: rgba(0, 0, 0, 0.02);
  }
  .body {
    margin-top: 60px;
    width: 100%;
    height: 440px;
    float: left;
    overflow-y: auto;
    background-color: rgba(0, 0, 0, 0.03);
    padding: 15px;

    .paper-item {
      width: 100%;
      height: auto;
      float: left;
      background-color: rgba(0, 0, 0, 0.02);
      padding: 15px;
      border-radius: 15px;
      margin-top: 10px;

      &:hover {
        cursor: pointer;
        background-color: rgba(0, 0, 0, 0.04);
      }

      .info {
        color: rgba(0, 0, 0, 0.4);
        font-size: 0.8rem;
        margin-bottom: 5px;
      }
      .content {
        color: #333;
      }
    }
  }
}
</style>
<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">指定试题</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Row>
          <Cell :width="12" class="banner">
            <div class="title">总分</div>
            <div class="value">{{ totalScore }}分</div>
          </Cell>
          <Cell :width="12" class="banner">
            <div class="title">试题</div>
            <div class="value">{{ data.length }}道</div>
          </Cell>
        </Row>
      </div>
      <div class="float-box mb-10">
        <Row :space="30">
          <Cell :width="12">
            <div class="paper-quesiton">
              <div class="title">已选择{{ data.length }}试题(点击试题可删除)</div>
              <div class="paper-item" @click="deleteQuestion(question)" v-for="question in data" :key="question.id">
                <div class="info">ID:{{ question.id }}|{{ question.type_text }}|{{ question.level_text }}|{{ question.score }}分</div>
                <div class="content" v-html="question.content"></div>
              </div>
            </div>
          </Cell>
          <Cell :width="12">
            <div class="question-box">
              <div class="filter-box">
                <Select v-model="category_id" :datas="categories" keyName="id" titleName="name" :filterable="true" @change="categoryChange"></Select>
              </div>
              <div class="body">
                <div class="paper-item" @click="addQuestion(question)" v-for="question in questions" :key="question.id">
                  <div class="info">ID:{{ question.id }}|{{ question.type_text }}|{{ question.level_text }}|{{ question.score }}分</div>
                  <div class="content" v-html="question.content"></div>
                </div>
              </div>
            </div>
          </Cell>
        </Row>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      questions: [],
      s: [],
      category_id: null,
      categories: [],
      data: [],
      loading: false
    };
  },
  mounted() {
    this.getData();
  },
  computed: {
    totalScore() {
      let score = 0;
      for (let i = 0; i < this.data.length; i++) {
        score += this.data[i].score;
      }
      return score;
    }
  },
  methods: {
    categoryChange() {
      this.getData();
    },
    getData() {
      R.Extentions.paper.Paper.Questions({
        id: this.id,
        category_id: this.category_id
      }).then(res => {
        this.questions = res.data.questions;
        this.categories = res.data.categories;
        this.data = res.data.data;
      });
    },
    deleteQuestion(question) {
      R.Extentions.paper.Paper.DelQuestion({ id: this.id, question_id: question.id }).then(res => {
        this.$Message.success('成功');
        this.getData();
      });
    },
    addQuestion(question) {
      R.Extentions.paper.Paper.AddQuestions({ id: this.id, s: [question.id] }).then(res => {
        this.$Message.success('成功');
        this.getData();
        this.s = [];
      });
    }
  }
};
</script>
