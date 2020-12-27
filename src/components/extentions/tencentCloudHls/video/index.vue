<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">腾讯云HLS视频加密</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form ref="form">
          <Row :space="10">
            <Cell :width="6">
              <FormItem label="状态">
                <Select v-model="filter.status" :datas="statusSelected" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="12">
              <FormItem label="课程">
                <Select v-model="filter.cid" :datas="courses" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <Button color="primary" @click="getData()">过滤</Button>
                <Button @click="resetFilter()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <div class="float-box mb-10">
        <p-del-button permission="addons.TencentCloudHls.videos.submitTransTask" text="批量提交" @click="multiSubmit()"></p-del-button>

        <p-del-button
          permission="addons.TencentCloudHls.createTemplate"
          :text="templateId ? '重置加密模板' : '创建加密转码模板'"
          @click="createTemplate()"
        ></p-del-button>
      </div>

      <div class="float-box mb-10">
        <Table :datas="datas" :checkbox="true" ref="table">
          <TableItem title="课程">
            <template slot-scope="{ data }">
              <span>{{ data.course.title }}</span>
            </template>
          </TableItem>
          <TableItem prop="title" title="视频"></TableItem>
          <TableItem prop="tencent_video_id" title="视频ID"></TableItem>
          <TableItem title="转码">
            <template slot-scope="{ data }">
              <span v-if="hlsVideoIds[data.id]">已转码</span>
              <span v-else>未转码</span>
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
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      datas: [],
      courses: [],
      hlsVideoIds: [],
      loading: false,
      filter: {
        status: 'no',
        cid: null
      },
      statusSelected: [
        {
          id: 'no',
          title: '未转码'
        },
        {
          id: 'yes',
          title: '已转码'
        }
      ],
      templateId: null
    };
  },
  mounted() {
    this.getData(true);
  },
  methods: {
    resetFilter() {
      this.filter.status = 'no';
      this.filter.cid = null;
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      let data = this.pagination;
      data.cid = this.filter.cid;
      data.status = this.filter.status;
      R.Extentions.tencentCloudHls.Videos.All(this.pagination).then(resp => {
        this.datas = resp.data.videos.data;
        this.pagination.total = resp.data.videos.total;
        this.hlsVideoIds = resp.data.hlsVideoIds;
        this.courses = resp.data.courses;
        this.templateId = resp.data.templateId;
      });
    },
    multiSubmit() {
      if (!this.templateId) {
        this.$Message.error('请先创建加密转码模板');
        return;
      }
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要转码的视频');
        return;
      }
      this.loading = true;
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.tencentCloudHls.Videos.SubmitTransTask({ ids: ids }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
        this.loading = false;
      });
    },
    createTemplate() {
      this.loading = true;
      R.Extentions.tencentCloudHls.Setting.createTemplate().then(res => {
        HeyUI.$Message.success('成功');
        this.loading = false;
        this.getData(true);
      });
    }
  }
};
</script>
