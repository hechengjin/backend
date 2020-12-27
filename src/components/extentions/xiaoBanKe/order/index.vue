<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">订单列表</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="6">
              <FormItem>
                <template v-slot:label>课程</template>
                <Select v-model="filter.course_id" :datas="courses" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <template v-slot:label>讲师</template>
                <Select v-model="filter.teacher_id" :datas="teachers" keyName="id" titleName="name" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <template v-slot:label>状态</template>
                <Select v-model="filter.set_teacher" :datas="setTeachersList" keyName="id" titleName="name"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData()">过滤</Button>
                <Button color="primary" @click="reset()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem title="用户" :width="200">
            <template slot-scope="{ data }">
              <span v-if="data.user">{{ data.user.nick_name }}</span>
              <span class="red" v-else>不存在</span>
            </template>
          </TableItem>
          <TableItem title="课程">
            <template slot-scope="{ data }">{{ data.course.title }}</template>
          </TableItem>
          <TableItem prop="charge" title="购买价格" unit="元" :width="120"></TableItem>
          <TableItem title="老师" :width="200">
            <template slot-scope="{ data }">
              <span v-if="data.teacher">{{ data.teacher.name }}</span>
              <p-button
                v-else
                glass="h-btn h-btn-primary"
                permission="addons.XiaoBanKe.order.setTeacher"
                text="安排教师"
                @click="setTeacher(data)"
              ></p-button>
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
  data() {
    return {
      datas: [],
      pagination: {
        page: 1,
        size: 10,
        total: 0,
        keywords: ''
      },
      loading: false,
      filter: {
        course_id: null,
        teacher_id: null,
        set_teacher: null
      },
      courses: [],
      teachers: [],
      setTeachersList: [
        {
          name: '全部',
          id: null
        },
        {
          name: '已设置讲师',
          id: 1
        },
        {
          name: '未设置讲师',
          id: 2
        }
      ]
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    reset() {
      this.filter.course_id = null;
      this.filter.set_teacher = null;
      this.filter.teacher_id = null;
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData() {
      this.loading = true;
      let data = this.pagination;
      data.course_id = this.filter.course_id;
      data.teacher_id = this.filter.teacher_id;
      data.set_teacher = this.filter.set_teacher;
      R.Extentions.xiaoBanKe.Order.Index(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.loading = false;
        this.courses = resp.data.courses;
        this.teachers = resp.data.teachers;
      });
    },
    setTeacher(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./teacher'], resolve);
          },
          datas: {
            id: item.id,
            course_id: item.course_id
          }
        },
        events: {
          success: (modal, data) => {
            HeyUI.$Message.success('成功');
            this.getData();
            modal.close();
          }
        }
      });
    }
  }
};
</script>
