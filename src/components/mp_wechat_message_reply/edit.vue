<template>
  <div class="h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">编辑</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">保存</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="message">
        <FormItem label="消息类型" prop="type">
          <Select v-model="message.type" :datas="types" keyName="id" titleName="name"></Select>
        </FormItem>

        <template v-if="message.type === 'text'">
          <FormItem label="匹配规则" prop="rule">
            <input type="text" v-model="message.rule" />
            <warn text="支持正则表达式" />
          </FormItem>
        </template>
        <template v-else-if="message.type === 'event'">
          <FormItem label="事件" prop="event_type">
            <Select v-model="message.event_type" :datas="eventTypes" keyName="id" titleName="name"></Select>
          </FormItem>
          <FormItem label="事件key" prop="event_key">
            <input type="text" v-model="message.event_key" />
          </FormItem>
        </template>

        <FormItem label="回复内容" prop="reply_content">
          <textarea v-model="message.reply_content" rows="3" placeholder="回复内容"></textarea>
        </FormItem>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      message: {
        type: null,
        event_type: null,
        event_key: null,
        rule: null,
        reply_content: null
      },
      rules: {
        required: ['type', 'reply_content']
      },
      types: [],
      eventTypes: [
        {
          id: 'subscribe',
          name: '用户关注'
        },
        {
          id: 'unsubscribe',
          name: '用户取消关注'
        },
        {
          id: 'SCAN',
          name: '二维码扫描事件'
        },
        {
          id: 'CLICK',
          name: '自定义菜单事件'
        }
      ]
    };
  },
  mounted() {
    R.MpWechatMessageReply.Create().then(res => {
      this.types = res.data.types;
    });
    R.MpWechatMessageReply.Edit({ id: this.id }).then(res => {
      this.message = res.data.data;
    });
  },
  methods: {
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        R.MpWechatMessageReply.Update(this.message).then(resp => {
          HeyUI.$Message.success('成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
