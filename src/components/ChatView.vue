<template>
  <div id="chat-view">
    <p class="msg" v-for="msg in messages">{{msg}}</p>
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
