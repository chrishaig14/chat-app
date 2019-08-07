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
      <login @login="login"/>
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
import io from 'socket.io-client'

export default {
  name: 'App',
  components: {ChatView, Login, Signup, ContactList},
  data () {
    return {
      currentUser: '',
      users: [],
      messages: ['hello, my friend!', 'hi pal, howdy?', 'fine, u?', 'oh great! u free next friday?', 'sure man, where do u wanna go?', 'how bout mcdonalds?', 'cool, see you there at 8pm', 'sure man, see ya'],
      contacts: [],
      socket: io('localhost:3000')
    }
  },
  methods: {
    newUser (user) {
      console.log('ADDED NEW USER')
      this.socket.emit('new:user', user)
      this.users.push({user: user})
    },
    openChat (id) {

    },
    addContact (id) {
      // this.contacts.push(id)
      this.socket.emit('add:contact', id)
    },
    login (id) {
      this.socket.emit('login', id)
      this.currentUser = id
    },
    getContacts () {
      this.socket.emit('get:contacts')
    }
  },
  mounted () {
    this.socket.on('login:ok', () => {
      console.log('LOGGED IN OK')
      this.socket.emit('get:contacts')
    })
    this.socket.on('login:error', () => {
      console.log('THERE WAS AN ERROR LOGGING IN')
    })
    this.socket.on('contacts', (m) => {
      this.contacts = m.contacts
      console.log('RECEIVED CONTACTS:', m)
    })
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
