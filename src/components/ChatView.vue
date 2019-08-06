<template>
  <div id="chat-view">
    <p class="msg" v-for="msg in messages">{{msg}}</p>
    <form @submit.prevent="handleSubmit">
      <label>Message:<input type="text"></label>
      <button>Send!</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'ChatView',
  props: {
    messages: Array
  },
  methods: {
    handleSubmit () {

    }
  }
}
</script>

<style scoped>
  #chat-view {
    background-color: lightgray;
  }

  input {
    background-color: white
  }

  .msg {
    border-radius: 5px;
    background-color: lightgreen;
  }

  form {
    display: flex;
    flex-direction: row;
  }
</style>
