<template>
  <div id="chat-view">
    <div ref="msglist" class="messages">
      <div class="msg"
           :class="[msg.user===currentUser?'myMessage':'otherMessage',msg.user===currentUser? (msg.ack?'msg-sent':'msg-pending'):'']"
           :key="msg.id" v-for="msg in messages">
        <div class="msg-user" style="color:#777; font-style: italic">
          {{msg.user}}
        </div>
        <!--        <p>-->
        <div>
          {{msg.content}}
        </div>
        Read by {{msg.read}}
        <!--        </p>-->
      </div>
    </div>
    <form @submit.prevent="handleSubmit">
      <input type="text" v-model="message">
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
    currentUser: String,
    messages: Array,
    new: Boolean,
    chatId: String
  },
  methods: {
    handleSubmit () {
      this.$emit('send:message', this.message)
      // this.messages.push({content: this.message, user: this.currentUser})
      this.message = ''
    },
    markAllAsRead () {
      console.log('######### CHAT UPDATED ########', this.messages)
      // console.log('MY PROPS ARE:', this.new, this.messages)
      this.$refs.msglist.scrollTop = this.$refs.msglist.scrollHeight
      let readMsgs = []

      for (let msg of this.messages) {
        if (!msg.read.includes(this.currentUser)) {
          readMsgs.push(msg.messageId)
        }
      }
      if (readMsgs.length !== 0) {
        // console.log('READMSGS: ', readMsgs)
        this.$emit('mark:read', {chatId: this.chatId, messageIds: readMsgs})
      }
    }
  },

  mounted () {
    console.log('MOUNTED CHAT VIEW: ', this.messages)
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
    /*align-items: center;*/
    padding: 1em;
    background-color: #c4c4c4;

  }

  /*form {*/
  /*  margin-top: 1em;*/
  /*}*/

  .messages {
    display: flex;
    flex-direction: column;
    /*align-content: center;*/
    align-items: center;
    overflow-y: auto;
    flex-grow: 1;
    margin-bottom: 1em;
    background-color: #a9a9a9;
    padding: 1em;
  }

  .msg {
    /*border-radius: 1.2rem;*/
    /*background-color: lightgreen;*/
    padding: 0.7rem;
    /*border: solid 1px green;*/
    /*text-align: center;*/
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
    /*margin-right: 1em;*/
    background-color: white;
    align-self: flex-end;
  }

  .otherMessage {
    /*margin-left: 1em;*/
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
    /*flex-grow:1;*/
  }

  form input {
    flex-grow: 1;
  }
</style>
