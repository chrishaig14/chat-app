<template>
  <div id="chat-view">
    <div class="messages">
      <p class="msg" v-for="msg in messages">{{msg}}</p>
    </div>
    <form @submit.prevent="handleSubmit">
      <label>Message:<input type="text" v-model="message"></label>
      <button>Send!</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'ChatView',
  data () {
    return {
      message: ''
    }
  },
  props: {
    messages: Array
  },
  methods: {
    handleSubmit () {
      this.$emit('send:message', this.message)
    }
  }
}
</script>

<style scoped>
  #chat-view {
    background-color: lightgray;
    display: flex;
    flex-direction: column;
    justify-content: stretch;
  }

  input {
    background-color: white
  }

  .messages {
    display: flex;
    flex-direction: column;
    /*align-content: center;*/
    align-items: center;
    overflow-y: auto;
    flex-grow: 1;
  }
  .msg {
    border-radius: 1.5rem;
    background-color: lightgreen;
    padding: 0.5rem;
    border: solid 1px green;
    text-align: center;
  }

  form {
    display: flex;
    flex-direction: row;
  }
</style>
