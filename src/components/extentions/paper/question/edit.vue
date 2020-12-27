<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button v-if="question.type === 1 || question.type === 2" color="primary" @click="addOption()">增加选项</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="question">
        <Row :space="10">
          <Cell width="6">
            <FormItem label="分类" prop="category_id">
              <template v-slot:label>分类</template>
              <Select v-model="question.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
            </FormItem>
          </Cell>
          <Cell width="6">
            <FormItem label="类型" prop="type">
              <template v-slot:label>类型</template>
              <Select v-model="question.type" :datas="types" keyName="id" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell width="6">
            <FormItem label="难度" prop="level">
              <template v-slot:label>难度</template>
              <Select v-model="question.level" :datas="levels" keyName="id" titleName="name"></Select>
            </FormItem>
          </Cell>
          <Cell width="6">
            <FormItem label="分数" prop="score">
              <template v-slot:label>分数</template>
              <input type="number" v-model="question.score" min="0" />
            </FormItem>
          </Cell>
        </Row>

        <FormItem label="问题内容" prop="content">
          <template v-slot:label>问题内容</template>
          <tinymce-editor v-model="question.content"></tinymce-editor>
        </FormItem>

        <template v-if="question.type === 1 || question.type === 2">
          <FormItem label="答案">
            <template v-slot:label>答案</template>
            <Select v-if="question.type === 2" v-model="choiceAnswer" :datas="choiceAnswers" :multiple="true" keyName="key" titleName="title">
              <template slot-scope="{ item }" slot="item">
                <div>{{ item.title }}</div>
              </template>
            </Select>
            <Select v-else v-model="question.answer" :datas="choiceAnswers" keyName="key" titleName="title">
              <template slot-scope="{ item }" slot="item">
                <div>{{ item.title }}</div>
              </template>
            </Select>
          </FormItem>
        </template>
        <template v-else-if="question.type === 5">
          <FormItem label="答案" prop="answer">
            <template v-slot:label>答案</template>
            <h-switch v-model="question.answer" trueValue="1" falseValue="0"></h-switch>
          </FormItem>
        </template>
        <template v-else>
          <FormItem label="答案" prop="answer" v-if="question.type !== 5">
            <template v-slot:label>答案</template>
            <textarea v-model="question.answer"></textarea>
            <warn text="如果是多选的话，多个答案请用英文逗号分开。如果是问答题的话，则无标准答案，需要人工阅卷。"></warn>
          </FormItem>
        </template>

        <FormItem label="解析" prop="remark">
          <template v-slot:label>解析</template>
          <tinymce-editor v-model="question.remark"></tinymce-editor>
        </FormItem>

        <template v-if="question.type === 1 || question.type === 2">
          <FormItem :prop="'option' + i" v-for="i in optionLength" :key="i">
            <template v-slot:label>选项{{ i }}</template>
            <wang-editor v-model="question['option' + i]"></wang-editor>
          </FormItem>
        </template>

        <FormItem>
          <Button color="primary" @click="create">保存</Button>
          <Button v-if="question.type === 1 || question.type === 2" color="primary" @click="addOption()">增加选项</Button>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
import TinymceEditor from '@/components/common/tinymce';
import WangEditor from '@/components/common/wangEditor';

export default {
  props: ['id'],
  components: { TinymceEditor, WangEditor },
  data() {
    return {
      question: {
        category_id: null,
        type: null,
        level: null,
        content: null,
        answer: null,
        score: null,
        option1: null,
        option2: null,
        option3: null,
        option4: null,
        option5: null,
        option6: null,
        option7: null,
        option8: null,
        option9: null,
        option10: null,
        remark: null
      },
      choiceAnswer: [],
      optionLength: 4,
      rules: {
        required: ['category_id', 'type', 'level', 'content', 'score']
      },
      levels: [],
      types: [],
      categories: []
    };
  },
  mounted() {
    this.init();
  },
  computed: {
    choiceAnswers() {
      let arr = [];
      for (let i = 1; i <= this.optionLength; i++) {
        let key = 'option' + i;
        let val = this.question[key];
        if (!val) {
          continue;
        }
        arr.push({
          key: 'option' + i,
          title: `选项${i}`
        });
      }
      return arr;
    }
  },
  methods: {
    addOption() {
      if (this.optionLength < 10) {
        this.optionLength++;
      } else {
        HeyUI.$Message.warn('最多支持10个选项');
      }
    },
    init() {
      R.Extentions.paper.Question.Create().then(res => {
        this.levels = res.data.levels;
        this.types = res.data.types;
        this.categories = res.data.categories;
      });

      R.Extentions.paper.Question.Edit({ id: this.id }).then(res => {
        this.question = res.data.data;
        this.levels = res.data.levels;
        this.types = res.data.types;
        this.categories = res.data.categories;

        for (let i = 5; i <= 10; i++) {
          if (this.question['option' + i] === null || this.question['option' + i].length === 0) {
            break;
          }
          this.optionLength++;
        }

        if (this.question.type === 2) {
          // 多选
          this.choiceAnswer = this.question.answer.split(',');
        }
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let data = this.question;
        if (data.type === 2) {
          data.answer = this.choiceAnswer.join(',');
        }
        R.Extentions.paper.Question.Update(data).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
