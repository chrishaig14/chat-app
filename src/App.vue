<template>
  <div id="app">
    <div>
      <signup @new:user="newUser"></signup>
      <p v-for="user in users" :key="user.user">{{user.user}}</p>
      <login @login="login"/>
      <p>Current user: <span style="font-weight: bold">{{currentUser}}</span>
      </p>
      <contact-list :contacts='contacts' @open:chat="openChat"
                    @add:contact="addContact"></contact-list>
    </div>
    <chat-view class="chat-view" @send:message="sendMessage"
               :messages='currentChat'/>
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
        messages: [],
        contacts: [],
        chats: {},
        currentUserChat: '',
        currentChat: [],
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
        console.log('OPENING CHAT WITH USER: ', id)
        this.currentChat = []
        this.currentUserChat = id
        this.socket.emit('get:chat', id)
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
      },
      sendMessage (m) {
        this.socket.emit('send:message', {
          contact: this.currentUserChat,
          message: m
        })
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
      this.socket.on('chat', (m) => {
        this.chats[m.user] = m
        this.currentChat = m.messages
        console.log('RECEIVED CHAT: ', typeof (m))
      })
      this.socket.on('new:message', (m) => {
        console.log('RECEIVED NEW MESSAGE: ', m)
        this.currentChat.push(m)
      })
    }
  }
</script>

<style>
  #app {
    display: flex;
    flex-direction: row;
    height: 100%;
  }

  button, input {
    outline: none;
  }

  button {
    border-radius: 1.5rem;
    border: solid 1px gray;
    padding: 0.5rem;
    background-color: greenyellow;
  }

  button:hover {
    background-color: lightgreen;
  }

  input[type="text"] {
    border-radius: 1.5rem;
    padding: 0.5rem;
    border: solid 1px gray;
  }
</style>
