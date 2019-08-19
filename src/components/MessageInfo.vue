<template>
  <div id="message-info">
    <button @click="$emit('close')" style="align-self: flex-end">Close</button>
    <div>
      Read by
      <div class="user-item" v-for="user in message.read" :key="user">{{user}}
      </div>
      Sent by {{message.user}}
    </div>
  </div>
</template>

<script>
export default {
  name: 'MessageInfo',
  props: {
    message: Object
  }
}
</script>

<style scoped>
  #message-info {
    background-color: #333333;
    color: white;
    display: flex;
    flex-direction: column;
  }

  .user-item {
    margin: 1em;
    background-color: #e7ffa3;
    color: black;
  }
</style>
