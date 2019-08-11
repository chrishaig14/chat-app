<template>
  <div id="app">
    <div class="side">
      <signup @new:user="newUser"></signup>
      <login @login="login"/>
      <p>Current user: <span style="font-weight: bold">{{currentUser}}</span>
      </p>
      <div>
        <h2>Chats</h2>
        <h3>New chat</h3>
        <new-chat class="new-chat" @new:chat="newChat"></new-chat>
        <div @click="openChat(chat)" class="chat-item"
             :class="[chats[chat].newMessages?'chat-item-new':'',chat===currentChat?'current-chat':'']"
             v-for="chat in Object.keys(chats)" :key="chat">
          {{chats[chat].type==='simple'?chats[chat].users.filter(user=>user!==currentUser).join(','):chats[chat].name}}
        </div>
      </div>
    </div>
    <chat-view v-if="currentChat!==''" :currentUser="currentUser"
               @reset:new="chats[currentChat].newMessages = false"
               class="chat-view"
               @send:message="sendMessage"
               :messages='chats[currentChat].messages'
               :new="chats[currentChat].newMessages"/>
  </div>
</template>

<script>
import Signup from '@/components/Signup'
import Login from '@/components/Login'
import ChatView from '@/components/ChatView'
import io from 'socket.io-client'
import NewChat from './components/NewChat'

let sqn = 0
export default {
  name: 'App',
  components: {NewChat, ChatView, Login, Signup},
  data () {
    return {
      currentUser: '',
      messages: [],
      msgs: {},
      chats: {},
      currentChat: '',
      socket: io('localhost:3000'),
      newMessages: {}
    }
  },
  methods: {
    newChat (chat) {
      console.log('NEW CHAT: ', chat)
      this.socket.emit('new:chat', chat)
    },
    newUser (user) {
      console.log('ADDED aNEW USER')
      this.socket.emit('new:user', user)
    },
    openChat (chat) {
      console.log('OPENING CHAT: ', chat)
      this.currentChat = chat
      this.socket.emit('get:chat', chat)
    },
    login (id) {
      this.socket.emit('login', id)
      this.currentUser = id
    },
    sendMessage (m) {
      this.socket.emit('send:message', {
        sqn: sqn,
        payload: {
          chatId: this.currentChat,
          content: m
        }
      })
      this.msgs[sqn] = {
        user: this.currentUser,
        chatId: this.currentChat,
        content: m
      }
      this.chats[this.currentChat].messages.push({
        user: this.currentUser,
        content: m,
        ack: false,
        sqn: sqn
      })
      console.log('EMIT EVENT: send:message ', this.msgs[sqn])
      sqn++
    }
  },
  mounted () {
    this.socket.on('login:ok', () => {
      console.log('LOGGED IN OK')
      this.socket.emit('get:contacts')
      this.socket.emit('get:all:chats')
    })
    this.socket.on('login:error', () => {
      console.log('THERE WAS AN ERROR LOGGING IN')
    })
    // this.socket.on('contacts', (m) => {
    //   console.log('RECEIVED CONTACTS:', m)
    //   this.contacts = m.contacts
    //   // for (let id of m.contacts) {
    //   //   let chatObj = {}
    //   //   chatObj[id] = {chatId: '', messages: [], newMessages: false}
    //   //   this.chats = Object.assign({}, this.chats, chatObj)
    //   //   console.log('ADDED CONTACT: ', this.chats)
    //   // }
    //
    // })
    this.socket.on('all:chats', (m) => {
      console.log('RECEIVED CHATSaaa: ', m)
      this.chats = Object.assign({}, this.chats, JSON.parse(JSON.stringify(m.chats)))
      console.log('CHATS::: ', m.chats)
    })
    this.socket.on('chat', (m) => {
      // this.chats[m.user] = m
      console.log('on chat', m)
      console.log('ALL CHATS ARE: ', this.chats)
      m.messages = m.messages.map(msg => ({
        ...msg, ack: true
      }))
      this.chats[m.chatId].messages = m.messages
      this.chats[m.chatId].newMessages = true
      console.log('ALL CHATS ARE: ', this.chats)
      // let c = this.chats[this.currentChat].messages
      // console.log('current chat messages', m)
    })
    this.socket.on('new:message', (m) => {
      console.log('on new:message', m)
      this.chats[m.chatId].messages.push(m.message)
      this.chats[m.chatId].newMessages = true
      // this.currentChat.push(m.message)
      // this.newMessages = true
    })
    this.socket.on('ack:message', (m) => {
      console.log('on ack:message')
      // let contact = this.msgs[m].contact
      let msg = this.msgs[m]
      console.log('RECEIVED ACK FOR MESSAGE: ', m)
      this.chats[msg.chatId].messages.map(message => {
        if (message.sqn === m) {
          message.ack = true
          return message
        }
        return message
      })
    })
  },
  updated () {
    let c = this.chats[this.currentChat].messages
    console.log('current chat messages', c)
  }
}
</script>

<style>
  #app {
    display: flex;
    flex-direction: row;
    height: 100%;
    color: white;
  }

  button, input {
    outline: none;
  }

  button {
    /*border-radius: 1.5rem;*/
    /*border: solid 1px gray;*/
    border: none;
    /*box-shadow: gray 0 2px 3px 0;*/
    padding: 0.5em;
    background-color: #e7ffa3;
  }

  .side {
    background-color: #333333;
    padding: 1em;
  }

  .chat-view {
    flex-grow: 1
  }

  .current-chat {
    background-color: red;
  }

  button:hover {
    background-color: #95a66a;
    /*box-shadow: gray 0 2px 3px 0;*/
  }

  button:active {
    box-shadow: none;
  }

  .chat-item {
    /*border-radius: 1em;*/
    background-color: #e7ffa3;
    margin-bottom: 1em;
    padding: 0.5em;
    color: black;
  }

  .chat-item-new {
    background-color: green;
  }

  .new-chat {
    margin-bottom: 1em;
  }

  input[type="text"] {
    /*border-radius: 1.5rem;*/
    border: none;
    padding: 0.5em;
    /*font-size: 14pt;*/
    /*border: solid 1px gray;*/
  }
</style>
