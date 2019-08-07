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
      <div class="contact" v-for="user in contacts" :key="user"
           @click="$emit('open:chat',user)">
        {{user}}
      </div>
    </div>
    <!--    <p v-for="user in users" :key="user.user">{{user.user}}</p>-->
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
    contacts: Array
  }
}
</script>

<style scoped>
  #contact-list {
    border: solid 1px gray;
  }

  /*h1 {}*/
  .list {
    display: flex;
    flex-direction: column;
    /*align-items: center;*/
  }
  .contact {
    margin-top: 0.5rem;
    background-color: whitesmoke;
    border-radius: 1.5rem;
    border: solid 1px gray;
    padding: 0.5rem;
  }

  .contact:hover {
    background-color: indianred;
  }
</style>
