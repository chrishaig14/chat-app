<template>
  <div id="chat-view">
    <div ref="msglist" class="messages">
      <div class="msg" :key="msg.id" v-for="msg in messages">
        {{msg.user}}:
        <p>
          {{msg.content}}
        </p>
      </div>
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
    messages: Array,
    new: Boolean
  },
  watch: {
    new: function (val) {
      if (val) {
        console.log('NEW MESSAGES!')
        this.$emit('reset:new')
      }
    }
  },
  methods: {
    handleSubmit () {
      this.$emit('send:message', this.message)
    }
  },
  mounted () {
    console.log('this.messages:', this.messages)
  },
  updated () {
    console.log('MY PROPS ARE:', this.new, this.messages)
    this.$refs.msglist.scrollTop = this.$refs.msglist.scrollHeight
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
