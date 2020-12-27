<style lang="less" scoped>
.question-item {
  width: 100%;
  height: auto;
  float: left;
  padding: 15px;
  border: 1px dashed #dddddd;

  .title {
    width: 100%;
    height: auto;
    float: left;
    line-height: 36px;
    font-weight: 600;
  }

  .content {
    width: 100%;
    height: auto;
    float: left;
    font-size: 12px;

    .option {
      width: 100%;
      height: auto;
      float: left;
      margin-right: 15px;
      margin-bottom: 15px;

      .option-item {
        width: 100%;
        height: auto;
        float: left;
        line-height: 20px;
        padding-left: 10px;
        margin-bottom: 2px;

        &.user-active {
          background-color: red;
          color: white;
        }

        &.active {
          background-color: rgba(0, 128, 0, 0.6);
          color: white;

          &.user-active {
            background-color: rgba(0, 128, 0, 1);
          }
        }

        img {
          max-width: 100px;
        }
      }
    }
  }

  .answer {
    width: 100%;
    height: auto;
    float: left;
    color: #333;
    line-height: 30px;
    background-color: rgba(0, 0, 0, 0.2);
    padding-left: 8px;
  }

  .result {
    width: 100%;
    height: auto;
    float: left;
    line-height: 30px;
  }

  .remark {
    width: 100%;
    height: auto;
    float: left;
    font-size: 12px;
    line-height: 18px;
    margin-top: 5px;
    margin-bottom: 5px;
  }

  .score {
    width: 100%;
    height: auto;
    float: left;
    margin-top: 10px;
  }
}

.green {
  color: green;
}
.red {
  color: red;
}

p {
  margin-top: 0;
  margin-bottom: 0;
}

.operator {
  width: 100%;
  height: auto;
  float: left;
  margin-top: 15px;
  text-align: right;
}
</style>
<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">详情</span>
      <div class="h-panel-right">
        <p-button
          v-if="userPaper.status === 3"
          glass="h-btn h-btn-primary"
          permission="addons.Paper.paper.userPaper.submit"
          text="提交阅卷结果"
          @click="submitPaper()"
        ></p-button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <h2>
          {{ userPaper.status_text }}
          <span v-if="userPaper.status === 2">- {{ userPaper.score }}分</span>
        </h2>
      </div>
      <div class="float-box mb-10">
        <div class="question-item" v-for="item in questions" :key="item.id">
          <template v-if="item.question">
            <div class="title">
              <span>{{ item.question.type_text }}</span>
              <span>({{ item.question.score }}分)</span>
              <span>{{ item.question.level_text }}</span>
            </div>
            <div class="content">
              <div class="mb-10" v-html="item.question.content"></div>
              <div class="option" v-if="item.question.type === 1 || item.question.type === 2">
                <template v-for="i in optionLength">
                  <div
                    class="option-item"
                    :class="{
                      active: selectIsActive('option' + i, item.question.answer),
                      'user-active': selectIsActive('option' + i, item.answer_content)
                    }"
                    v-if="item.question['option' + i]"
                    :key="i"
                    v-html="item.question['option' + i]"
                  ></div>
                </template>
              </div>
            </div>
            <div class="answer">
              <p v-if="item.question.type !== 1 && item.question.type !== 2">回答：{{ userAnswer(item) }}</p>
              <template v-if="item.thumbs_rows.length > 0">
                <img
                  v-for="(img, index) in item.thumbs_rows"
                  :key="index"
                  :src="img"
                  width="70"
                  height="70"
                  @click="imagePreview(index, item.thumbs_rows)"
                />
              </template>
            </div>
            <div class="result" v-if="item.question.type !== 4">
              <p :class="item.is_correct ? 'green' : 'red'">{{ item.is_correct ? '正确' : '错误' }} | 本题得分：{{ item.score }}分</p>
            </div>
            <div class="remark" v-if="item.question.remark">
              <p>解析：</p>
              <p v-html="item.question.remark"></p>
            </div>
            <div class="score" v-if="userPaper.status === 3 && item.question.type === 4">
              <p>请打分：</p>
              <Select v-model="item.score" :datas="scoreList(item.question.score)" keyName="id" titleName="text"></Select>
            </div>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ['paper_id', 'user_paper_id'],
  data() {
    return {
      userPaper: {},
      questions: [],
      optionLength: 10
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    scoreList(max) {
      var rows = [];
      for (var i = 1; i <= max; i++) {
        rows.push({
          id: i,
          text: i + '分'
        });
      }
      return rows;
    },
    selectIsActive(val, answer) {
      var answers = answer.split(',');
      return answers.indexOf(val) !== -1;
    },
    userAnswer(item) {
      if (item.question.type === 1) {
        return item.question[item.answer_content];
      } else if (item.question.type === 2) {
        let rows = [];
        item.answer_content.split(',').forEach(i => {
          rows.push(item.question[i]);
        });
        return rows.join(',');
      } else if (item.question.type === 5) {
        return parseInt(item.answer_content) === 1 ? '正确' : '错误';
      } else {
        return item.answer_content;
      }
    },
    init() {
      R.Extentions.paper.Paper.UserPaperInfo({
        id: this.paper_id,
        user_paper_id: this.user_paper_id
      }).then(res => {
        this.userPaper = res.data.userPaper;
        this.questions = res.data.questions;
      });
    },
    submitPaper() {
      var data = {};
      for (var i = 0; i < this.questions.length; i++) {
        var item = this.questions[i];
        if (item.question.type !== 4) {
          // 过滤非问答题
          continue;
        }

        data[item.id] = {
          score: item.score
        };
      }

      R.Extentions.paper.Paper.SubmitScore({
        id: this.paper_id,
        user_paper_id: this.user_paper_id,
        data: data
      }).then(res => {
        HeyUI.$Message.success('评卷成功，' + res.data.nickname + '得分：' + res.data.total_score + '分');
        this.$emit('success');
      });
    },
    imagePreview(index, items) {
      this.$ImagePreview(items, index);
    }
  }
};
</script>
