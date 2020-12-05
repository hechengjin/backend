<style lang="less"></style>
<template>
  <div class>
    <div class="table-basic-vue frame-page h-panel">
      <div class="h-panel-bar">
        <span class="h-panel-title">添加课程</span>
      </div>
      <div class="h-panel-body">
        <Form
          mode="block"
          ref="form"
          :validOnChange="true"
          :showErrorTip="true"
          :rules="rules"
          :model="course"
        >
          <FormItem label="类型" prop="type">
            <template v-slot:label>类型</template>
            <Select v-model="course.type" :datas="courseTypes" keyName="id" titleName="name"></Select>
          </FormItem>
          <FormItem label="上限人数" prop="max_people_num">
            <template v-slot:label>上限人数</template>
            <div class="h-input-group" v-width="200">
              <input type="text" v-model="course.max_people_num" disabled />
              <span class="h-input-addon">人</span>
            </div>
            <warn text="小班课推荐最多6人，大班课推荐最多50人，填写0表示不限制"></warn>
          </FormItem>
          <FormItem label="分类" prop="category_id">
            <template v-slot:label>分类</template>
            <Select
              v-model="course.category_id"
              :datas="categories"
              keyName="id"
              titleName="name"
              :filterable="true"
            ></Select>
          </FormItem>
          <FormItem label="老师" prop="teacher_ids">
            <template v-slot:label>老师</template>
            <Select
              v-model="course.teacher_ids"
              :datas="teachers"
              :multiple="true"
              keyName="id"
              titleName="name"
              :filterable="true"
            ></Select>
          </FormItem>
          <FormItem label="课程标题" prop="title">
            <template v-slot:label>课程标题</template>
            <input type="text" v-model="course.title" />
          </FormItem>
          <FormItem label="开课时间" prop="start_at">
            <template v-slot:label>开课时间</template>
            <DatePicker v-model="course.start_at" type="datetime"></DatePicker>
          </FormItem>
          <FormItem label="课程封面" prop="thumb">
            <template v-slot:label>课程封面</template>
            <image-upload v-model="course.thumb" name="课程封面"></image-upload>
          </FormItem>
          <FormItem label="价格" prop="charge">
            <template v-slot:label>价格</template>
            <div class="h-input-group" v-width="200">
              <input type="text" v-model="course.charge" />
              <span class="h-input-addon">元</span>
            </div>
          </FormItem>
          <FormItem label="原价" prop="original_charge">
            <template v-slot:label>原价</template>
            <div class="h-input-group" v-width="200">
              <input type="text" v-model="course.original_charge" />
              <span class="h-input-addon">元</span>
            </div>
          </FormItem>
          <FormItem label="简短介绍" prop="short_desc">
            <template v-slot:label>简短介绍</template>
            <textarea v-model="course.short_desc"></textarea>
          </FormItem>
          <FormItem label="详细介绍" prop="description">
            <template v-slot:label>详细介绍</template>
            <tinymce-editor v-model="course.original_desc"></tinymce-editor>
          </FormItem>
          <FormItem>
            <Button color="primary" @click="create">添加</Button>
          </FormItem>
        </Form>
      </div>
    </div>
  </div>
</template>
<script>
import TinymceEditor from '../../../common/tinymce';

export default {
  components: {
    TinymceEditor
  },
  data() {
    return {
      course: {
        type: null,
        category_id: null,
        title: '',
        thumb: '',
        charge: null,
        original_charge: null,
        short_desc: '',
        original_desc: '',
        is_show: 1,
        max_people_num: null,
        start_at: null,
        teacher_ids: null
      },
      rules: {
        required: [
          'start_at',
          'teacher_ids',
          'type',
          'category_id',
          'title',
          'thumb',
          'charge',
          'original_charge',
          'short_desc',
          'original_desc',
          'is_show',
          'max_people_num'
        ]
      },
      teachers: [],
      categories: [],
      courseTypes: [
        {
          id: 0,
          name: '小班课'
        },
        {
          id: 1,
          name: '大班课'
        },
        {
          id: 2,
          name: '直播课'
        },
        {
          id: 3,
          name: '1v1'
        }
      ]
    };
  },
  watch: {
    'course.type'() {
      if (this.course.type === 0) {
        this.course.max_people_num = 6;
      } else if (this.course.type === 1) {
        // 大班课
        this.course.max_people_num = 200;
      } else if (this.course.type === 2) {
        // 直播课
        this.course.max_people_num = 0;
      } else if (this.course.type === 3) {
        // 1v1
        this.course.max_people_num = 1;
      }
    }
  },
  mounted() {
    R.Extentions.xiaoBanKe.Course.Create().then(res => {
      this.teachers = res.data.teachers;
      this.categories = res.data.categories;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let data = this.course;
        data.render_desc = this.course.original_desc;
        R.Extentions.xiaoBanKe.Course.Store(data).then(() => {
          this.$emit('success');
        });
      }
    }
  }
};
</script>
