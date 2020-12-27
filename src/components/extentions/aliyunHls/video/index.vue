<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">阿里云HLS视频加密</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form ref="form">
          <Row :space="10">
            <Cell width="6">
              <FormItem label="状态">
                <Select v-model="filter.status" :datas="statusSelected" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell width="12">
              <FormItem label="课程">
                <Select v-model="filter.cid" :datas="courses" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell width="6">
              <FormItem>
                <Button color="primary" @click="getData()">过滤</Button>
                <Button @click="resetFilter()">重置</Button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>
      <div class="float-box mb-10">
        <p-del-button permission="video.aliyun_hls.submit" @click="multiSubmit()" text="批量提交"></p-del-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas" :checkbox="true" ref="table">
          <TableItem title="课程">
            <template slot-scope="{ data }">
              <span>{{ data.course.title }}</span>
            </template>
          </TableItem>
          <TableItem prop="title" title="视频"></TableItem>
          <TableItem prop="aliyun_video_id" title="视频ID"></TableItem>
          <TableItem title="转码">
            <template slot-scope="{ data }">
              <span v-if="hlsVideoIds[data.id]">已转码</span>
              <span v-else>未转码</span>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="200">
            <template slot-scope="{ data }">
              <p-del-button permission="video.aliyun_hls.submit" @click="remove(datas, data)" text="提交转码"></p-del-button>
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
        size: 20,
        total: 0
      },
      datas: [],
      courses: [],
      hlsVideoIds: [],
      loading: false,
      selectedIds: [],
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
      ]
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    resetFilter() {
      this.filter.status = 'no';
      this.filter.cid = null;
      this.getData();
    },
    init() {
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      data.status = this.filter.status;
      data.cid = this.filter.cid;
      R.Extentions.aliyunHls.Videos.All(this.pagination).then(resp => {
        this.datas = resp.data.videos.data;
        this.pagination.total = resp.data.videos.total;
        this.loading = false;
        this.hlsVideoIds = resp.data.hlsVideoIds;
        this.courses = resp.data.courses;
      });
    },
    remove(data, item) {
      R.Extentions.aliyunHls.Videos.SubmitTransTask({ ids: [item.id] }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    },
    multiSubmit() {
      let items = this.$refs.table.getSelection();
      if (items.length === 0) {
        this.$Message.error('请选择需要提交转码的视频');
        return;
      }
      let ids = [];
      for (let i = 0; i < items.length; i++) {
        ids.push(items[i].id);
      }
      R.Extentions.aliyunHls.Videos.SubmitTransTask({ ids: ids }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    }
  }
};
</script>
