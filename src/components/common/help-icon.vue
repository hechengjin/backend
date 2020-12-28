<template>
  <i class="h-icon-help" v-tooltip trigger="click" :content="text"></i>
</template>
<script>
export default {
  props: ['text']
};
</script>