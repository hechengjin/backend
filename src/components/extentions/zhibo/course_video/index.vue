<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">内容安排</span>
      <div class="h-panel-right">
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <p-button glass="h-btn h-btn-primary" permission="addons.Zhibo.course_video.store" text="添加" @click="create()"></p-button>
      </div>
      <div class="float-box mb-10">
        <Table :loading="loading" :datas="datas">
          <TableItem title="标题">
            <template slot-scope="{ data }">
              <span class="grey">{{ data.course.title }}</span>
              <span>/</span>
              <span class="grey">{{ data.chapter.name }}</span>
              <span>/</span>
              <span>{{ data.title }}</span>
            </template>
          </TableItem>
          <TableItem prop="published_at" title="直播时间" :width="160"></TableItem>
          <TableItem prop="status_text" title="状态" :width="100"></TableItem>
          <TableItem title="显示" :width="50">
            <template slot-scope="{ data }">
              <span v-if="data.is_show === 1">是</span>
              <span v-else>否</span>
            </template>
          </TableItem>
          <TableItem title="操作" align="center" :width="300">
            <template slot-scope="{ data }">
              <p-del-button permission="addons.Zhibo.course_video.delete" @click="remove(datas, data)"></p-del-button>
              <p-button glass="h-btn h-btn-s h-btn-primary" permission="addons.Zhibo.course_video.update" text="编辑" @click="edit(data)"></p-button>
              <p-button
                v-if="data.status === 1"
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.Zhibo.zhibo.pause"
                text="停止直播"
                @click="livePause(data)"
              ></p-button>
              <p-button
                v-if="data.status !== 0"
                glass="h-btn h-btn-s h-btn-primary"
                permission="addons.Zhibo.course_video.watch.users"
                text="观看用户"
                @click="watchUsers(data)"
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
  props: ['course_id'],
  data() {
    return {
      datas: [],
      pagination: {
        page: 1,
        size: 10,
        total: 0,
        keywords: '',
        course_id: this.course_id
      },
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
    getData(reset = false) {
      if (reset) {
        this.pagination.page = 1;
        this.pagination.keywords = '';
      }
      this.loading = true;
      R.Extentions.zhibo.CourseVideo.List(this.pagination).then(resp => {
        this.datas = resp.data.data;
        this.pagination.total = resp.data.total;
        this.loading = false;
      });
    },
    create() {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./create'], resolve);
          },
          datas: {
            course_id: this.course_id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData(true);
          }
        }
      });
    },
    remove(data, item) {
      R.Extentions.zhibo.CourseVideo.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    },
    edit(item) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./edit'], resolve);
          },
          datas: {
            id: item.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData(true);
          }
        }
      });
    },
    livePause(video) {
      R.Extentions.zhibo.Zhibo.pause({ video_id: video.id }).then(res => {
        HeyUI.$Message.success('成功');
        this.getData();
      });
    },
    watchUsers(video) {
      this.$Modal({
        hasCloseIcon: true,
        closeOnMask: false,
        component: {
          vue: resolve => {
            require(['./watch_users'], resolve);
          },
          datas: {
            course_id: video.course_id,
            video_id: video.id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.getData(true);
          }
        }
      });
    }
  }
};
</script>
