<template>
  <Button color="primary" @click="showModal()">{{ nickname ? nickname : '请选择用户' }}</Button>
</template>
<script>
export default {
  props: {
    value: {
      type: Number,
      default: ''
    }
  },
  data() {
    return {
      user_id: this.value,
      nickname: null
    };
  },
  mounted() {},
  watch: {
    value(newVal, oldVal) {
      this.user_id = newVal;
      if (!this.user_id) {
        this.nickname = null;
      }
    },
    user_id(newVal, oldVal) {
      this.$emit('input', newVal);
    }
  },
  methods: {
    showModal() {
      this.$Modal({
        closeOnMask: false,
        hasCloseIcon: true,
        component: {
          vue: resolve => {
            require(['./modal'], resolve);
          },
          datas: {
            id: this.user_id
          }
        },
        events: {
          success: (modal, data) => {
            modal.close();
            this.user_id = data.id;
            this.nickname = data.nickname;
          }
        }
      });
    }
  }
};
</script>