<template>
  <div id="chat-view">
    <div ref="msglist" class="messages">
      <message v-for="msg in messages" :key="msg.id" :message="msg"
               :current-user="currentUser"/>
    </div>
    <form @submit.prevent="handleSubmit">
      <input type="text" v-model="message">
      <button>Send!</button>
    </form>
  </div>
</template>

<script>

import Message from './Message'

export default {
  name: 'ChatView',
  components: {Message},
  data () {
    return {
      message: ''
    }
  },
  props: {
    currentUser: String,
    messages: Array,
    new: Boolean,
    chatId: String
  },
  methods: {
    handleSubmit () {
      this.$emit('send:message', this.message)
      this.message = ''
    },
    markAllAsRead () {
      this.$refs.msglist.scrollTop = this.$refs.msglist.scrollHeight
      let readMsgs = []

      for (let msg of this.messages) {
        if (!msg.read.includes(this.currentUser)) {
          readMsgs.push(msg.messageId)
        }
      }
      if (readMsgs.length !== 0) {
        this.$emit('mark:read', {chatId: this.chatId, messageIds: readMsgs})
      }
    }
  },

  mounted () {
    this.markAllAsRead()
  },

  updated () {
    this.markAllAsRead()
  }
}
</script>

<style scoped>
  #chat-view {

    display: flex;
    flex-direction: column;
    justify-content: stretch;
    padding: 1em;
    background-color: #c4c4c4;

  }

  .messages {
    display: flex;
    flex-direction: column;
    align-items: center;
    overflow-y: auto;
    flex-grow: 1;
    margin-bottom: 1em;
    background-color: #a9a9a9;
    padding: 1em;
  }

  .msg {
    padding: 0.7rem;
    font-size: 0.7em;
    margin-bottom: 1em;
    max-width: 60%;
    color: black;
    word-break: break-all;
  }

  .msg-user {
    margin-bottom: 1em;
  }

  .myMessage {
    background-color: white;
    align-self: flex-end;
  }

  .otherMessage {
    background-color: #e7ffa3;
    align-self: flex-start;
  }

  .msg-sent {
    background-color: white;
  }

  .msg-pending {
    background-color: #ffff80;
  }

  form {
    display: flex;
    flex-direction: row;
  }

  form input {
    flex-grow: 1;
  }
</style>
