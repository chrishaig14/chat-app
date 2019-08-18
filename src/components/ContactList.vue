<template>
  <div id="contact-list">
    <h1>Contacts</h1>
    <div>
      <form @submit.prevent="$emit('add:contact',newContact)">
        <label>New contact:<input type="text" v-model="newContact"></label>
        <button>Add</button>
      </form>
    </div>
    <div class="list">
      <div class="contact" :class="{'new-msg':chats[contact].newMessages}"
           v-for="contact in contacts" :key="contact"
           @click="$emit('open:chat',contact)">
        {{contact}}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ContactList',
  data () {
    return {
      newContact: ''
    }
  },
  props: {
    contacts: Array,
    chats: Object
  }
}
</script>

<style scoped>
  .new-msg {
    background-color: green
  }

  #contact-list {
    border: solid 1px gray;
  }

  .list {
    display: flex;
    flex-direction: column;
  }

  .contact {
    margin-top: 0.5rem;
    padding: 0.5rem;
  }

  .contact:hover {
    background-color: indianred;
  }
</style>
