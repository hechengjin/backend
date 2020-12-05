<style lang="less" scoped>
.w-400 {
  width: 400px;
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar">
      <span class="h-panel-title">开始直播</span>
    </div>
    <div class="h-panel-body">
      <div class="float-box mb-10">
        <Form>
          <Row :space="10">
            <Cell :width="24">
              <FormItem label="课程">
                <Select v-model="course_id" :datas="courses" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="12">
              <FormItem label="课程安排">
                <Select v-model="video_id" :datas="videos[course_id]" keyName="id" titleName="title" :filterable="true"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem label="服务商">
                <Select v-model="service" :datas="services" keyName="id" titleName="title"></Select>
              </FormItem>
            </Cell>
            <Cell :width="6">
              <FormItem>
                <p-button glass="h-btn h-btn-primary" permission="addons.Zhibo.zhibo.open" text="开始直播" @click="create()"></p-button>
              </FormItem>
            </Cell>
          </Row>
        </Form>
      </div>

      <template v-if="push_url">
        <div class="float-box mb-10">
          <div class="h-input-group mb-10 w-400">
            <span class="h-input-addon">推流地址</span>
            <input type="text" v-model="push_url" />
            <Button color="primary" @click="$Clipboard({ text: push_url })">复制</Button>
          </div>
          <div class="h-input-group mb-10 w-400">
            <span class="h-input-addon">OBS服务器</span>
            <input type="text" v-model="obs.server" />
            <Button color="primary" @click="$Clipboard({ text: obs.server })">复制</Button>
          </div>
          <div class="h-input-group mb-10 w-400">
            <span class="h-input-addon">OBS串流密钥</span>
            <input type="text" v-model="obs.token" />
            <Button color="primary" @click="$Clipboard({ text: obs.token })">复制</Button>
          </div>
        </div>
        <div class="float-box mb-10">
          <p-button glass="h-btn h-btn-primary" permission="addons.Zhibo.zhibo.pause" text="停止直播" @click="pause()"></p-button>

          <p-button glass="h-btn h-btn-primary" permission="addons.Zhibo.zhibo.resume" text="恢复直播" @click="resume()"></p-button>
        </div>
      </template>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      course_id: null,
      video_id: null,
      courses: [],
      videos: [],
      push_url: '',
      service: null,
      services: [
        {
          title: '腾讯云直播',
          id: 'tencent'
        },
        {
          title: '阿里云直播',
          id: 'aliyun'
        }
      ],
      obs: {
        server: '',
        token: ''
      }
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Extentions.zhibo.Zhibo.Create().then(res => {
        this.courses = res.data.courses;
        this.videos = res.data.videos;
      });
    },
    create() {
      if (!this.video_id) {
        HeyUI.$Message.warn('请选择课程安排');
        return;
      }
      if (!this.service) {
        HeyUI.$Message.warn('请选择直播服务商');
        return;
      }
      this.obs.server = '';
      this.obs.token = '';
      R.Extentions.zhibo.Zhibo.getParams({ video_id: this.video_id, service: this.service }).then(res => {
        this.push_url = res.data.push_url;
        let obs = this.push_url.split('/');
        for (let i = 0; i < obs.length; i++) {
          if (i === obs.length - 1) {
            this.obs.token = obs[i];
          } else {
            this.obs.server = this.obs.server + obs[i] + '/';
          }
        }
      });
    },
    pause() {
      R.Extentions.zhibo.Zhibo.pause({ video_id: this.video_id, service: this.service }).then(res => {
        HeyUI.$Message.success('成功');
      });
    },
    resume() {
      R.Extentions.zhibo.Zhibo.resume({ video_id: this.video_id, service: this.service }).then(res => {
        HeyUI.$Message.success('成功');
      });
    }
  }
};
</script>
