<template>
  <div class="msg" @click="$emit('message:click',message.messageId)"
       :class="[message.user===currentUser?'myMessage':'otherMessage',message.user===currentUser? (message.ack?'msg-sent':'msg-pending'):'']"
       :key="message.messageId">
    <div class="msg-user" style="color:#777; font-style: italic">
      {{message.user}}
    </div>
    <div style="margin-bottom: 0.5em; margin-top:0.5em;">
      {{message.content}}
    </div>
    <div style="color:gray">{{message.hasOwnProperty('timestamp')?new
      Date(message.timestamp).toLocaleString():'Sending'}}
    </div>
  </div>
</template>

<script>
export default {
  name: 'Message',
  props: {
    message: Object,
    currentUser: String
  },
  mounted () {
    console.log('MESSAGE: ', this.message)
  }
}
</script>

<style scoped>

</style>
