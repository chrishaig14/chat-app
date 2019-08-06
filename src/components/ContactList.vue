<template>
  <div id="contact-list">
    <div class="contact" v-for="user in contacts" :key="user"
         @click="$emit('open:chat',user)">
      {{user}}
    </div>
    <!--    <p v-for="user in users" :key="user.user">{{user.user}}</p>-->
  </div>
</template>

<script>
export default {
  name: 'ContactList',
  data () {
    return {}
  },
  props: {
    contacts: Array
  }
}
</script>

<style scoped>
  .contact {
    background-color: whitesmoke;
    border-radius: 5px;
    border: solid 1px gray;
  }
</style>
