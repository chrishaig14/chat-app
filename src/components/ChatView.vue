<template>
  <div id="chat-view">
    <p class="msg" v-for="msg in messages">{{msg}}</p>
  </div>
</template>

<script>
export default {
  name: 'ChatView',
  props: {
    messages: Array
  }
}
</script>

<style scoped>
  .msg {
    border-radius: 5px;
    background-color: lightgreen;
  }
</style>
