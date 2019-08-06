<template>
  <div id="app">
    <!--    <img src="./assets/logo.png">-->
    <!--    &lt;!&ndash;    <router-view/>&ndash;&gt;-->
    <!--    <h1>Employees</h1>-->
    <!--    <employee-form @add:employee="addEmployee"-->
    <!--    />-->
    <!--    <employee-table :employees="employees" @edit:employee="editEmployee"-->
    <!--                    @delete:employee="deleteEmployee"/>-->
    <div>
      <signup @new:user="newUser"></signup>
      <p v-for="user in users" :key="user.user">{{user.user}}</p>
      <login/>
      <contact-list :contacts='contacts' @open:chat="openChat"
                    @add:contact="addContact"></contact-list>
    </div>
    <chat-view :messages='messages'/>
  </div>
</template>

<script>
import ContactList from '@/components/ContactList'
import Signup from '@/components/Signup'
import Login from '@/components/Login'
import ChatView from '@/components/ChatView'

export default {
  name: 'App',
  components: {ChatView, Login, Signup, ContactList},
  data () {
    return {
      users: [],
      messages: ['hello, my friend!', 'hi pal, howdy?', 'fine, u?', 'oh great! u free next friday?', 'sure man, where do u wanna go?', 'how bout mcdonalds?', 'cool, see you there at 8pm', 'sure man, see ya'],
      contacts: ['chris', 'alex', 'john', 'amy', 'rachel']
    }
  },
  methods: {
    newUser (user) {
      console.log('ADDED NEW USER')
      this.users.push({user: user})
    },
    openChat (id) {

    },
    addContact (id) {
      this.contacts.push(id)
    }
  }
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    display: flex;
    flex-direction: row;
  }
</style>
