<template>
  <div class="h-panel w-1000">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="paper">
        <Row :space="10">
          <Cell :width="18">
            <FormItem label="标题" prop="title">
              <input type="text" v-model="paper.title" placeholder="试卷标题" />
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="分类" prop="category_id">
              <Select v-model="paper.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
            </FormItem>
          </Cell>

          <Cell :width="24">
            <FormItem label="封面" prop="thumb">
              <image-upload v-model="paper.thumb" name="封面"></image-upload>
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="分数" prop="score">
              <input type="number" v-model="paper.score" placeholder="分数" />
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="及格分" prop="pass_score">
              <input type="number" v-model="paper.pass_score" placeholder="及格分" />
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="时间" prop="expired_minutes">
              <input type="number" v-model="paper.expired_minutes" min="0" placeholder="单位：分钟" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="可重复考试次数" prop="try_times">
              <input type="number" v-model="paper.try_times" min="0" placeholder="填写0意味着不限制" />
            </FormItem>
          </Cell>
        </Row>
        <Row :sapce="10">
          <Cell :width="6">
            <FormItem label="试题随机" prop="is_random">
              <h-switch v-model="paper.is_random" :trueValue="1" :falseValue="0"></h-switch>
            </FormItem>
          </Cell>
          <Cell :width="18" v-if="paper.is_random === 1">
            <FormItem label="试题随机范围" prop="random_category_id">
              <Select v-model="paper.random_category_id" :datas="questionCategories" keyName="id" titleName="name" :filterable="true"></Select>
              <warn text="试题随机的话，将会按照试卷的总分随机抽出一定的试题。"></warn>
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="6">
            <FormItem label="仅邀请" prop="enabled_invite">
              <h-switch v-model="paper.enabled_invite" :trueValue="1" :falseValue="0"></h-switch>
              <warn text="只有在后台添加的用户才可以参与考试。"></warn>
            </FormItem>
          </Cell>
          <template v-if="paper.enabled_invite === 0">
            <Cell :width="6">
              <FormItem label="免费" prop="is_free">
                <h-switch v-model="paper.is_free" :trueValue="1" :falseValue="0"></h-switch>
                <warn text="所有人都可以参与考试。"></warn>
              </FormItem>
            </Cell>
          </template>
        </Row>

        <Row :space="10" v-if="paper.is_free === 0">
          <Cell :width="6">
            <FormItem label="价格" prop="charge">
              <input type="number" v-model="paper.charge" />
              <warn text="价格大于0的话用户可以购买此试卷参与考试"></warn>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="会员免费" prop="is_vip_free">
              <h-switch v-model="paper.is_vip_free" :trueValue="1" :falseValue="0"></h-switch>
              <warn text="VIP用户可免费参与考试"></warn>
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="必须购买课程" prop="required_courses">
              <template v-slot:label>必须购买课程</template>
              <Select v-model="paper.required_courses" :datas="courses" :multiple="true" keyName="id" titleName="title" :filterable="true"></Select>
              <warn text="购买其中一门课程即可参与考试"></warn>
            </FormItem>
          </Cell>
        </Row>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      paper: {
        category_id: null,
        title: null,
        thumb: null,
        score: null,
        pass_score: null,
        expired_minutes: null,
        is_random: 0,
        try_times: null,
        is_vip_free: 0,
        is_free: 0,
        charge: 0,
        random_rule: null,
        random_category_id: null,
        required_courses: [],
        enabled_invite: 0
      },
      rules: {
        required: [
          'category_id',
          'title',
          'thumb',
          'score',
          'pass_score',
          'expired_minutes',
          'is_random',
          'try_times',
          'is_vip_free',
          'is_free',
          'enabled_invite'
        ]
      },
      createParams: {},
      categories: [],
      questionCategories: [],
      courses: []
    };
  },
  mounted() {
    this.paper.enabled_invite = 1;
    this.paper.is_free = 1;

    this.init();
  },
  methods: {
    init() {
      R.Extentions.paper.Paper.Create().then(res => {
        this.categories = res.data.categories;
        this.questionCategories = res.data.questionCategories;
        this.courses = res.data.courses;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let data = this.paper;
        data.required_courses && (data.required_courses = data.required_courses.join(','));
        R.Extentions.paper.Paper.Store(data).then(resp => {
          HeyUI.$Message.success('添加成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
