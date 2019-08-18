<template>
  <div class="msg"
       :class="[message.user===currentUser?'myMessage':'otherMessage',message.user===currentUser? (message.ack?'msg-sent':'msg-pending'):'']"
       :key="message.id">
    <div class="msg-user" style="color:#777; font-style: italic">
      {{message.user}}
    </div>
    <div>
      {{message.content}}
    </div>
    Read by {{message.read}}
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
