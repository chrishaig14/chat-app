<template>
  <div id="app">
    <div class="side">
      <signup @new:user="newUser"></signup>
      <login @login="login"/>
      <p>Current user: <span style="font-weight: bold">{{currentUser}}</span>
      </p>
      <div>
        <h2>CHATS</h2>
        <button @click="show_new_chat=true" style="margin-bottom: 1em;">Create
          new chat
        </button>
        <!--        <h3>New chat</h3>-->

        <new-chat v-if="show_new_chat" :suggestions="suggestions"
                  @suggest:users='suggestUsers'
                  class="new-chat"
                  @new:chat="newChat" @cancel="show_new_chat=false"/>
        <div v-else @click="openChat(chat)" class="chat-item"
             :class="chat===currentChat?'current-chat':'other-chat'"
             v-for="chat in Object.keys(chats)" :key="chat">
          {{chats[chat].type==='simple'?chats[chat].users.filter(user=>user!==currentUser).join(','):chats[chat].name}}
          <div
            style="display:inline-block;border-radius: 50%;width:10px;background-color: green;height:10px;"
            v-if="chats[chat].newMessages"></div>
        </div>
      </div>
    </div>
    <chat-view :chatId="currentChat" @mark:read="markRead"
               v-if="currentChat!==''"
               :currentUser="currentUser"
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
      show_new_chat: false,
      currentUser: '',
      messages: [],
      chats: {},
      currentChat: '',
      socket: io('localhost:3000'),
      suggestions: []
    }
  },
  methods: {
    suggestUsers (str) {
      this.socket.emit('suggest:users', str, (result) => {
        this.suggestions = result
      })
    },
    markRead (data) {
      this.socket.emit('mark:read', data)
    },
    newChat (chat) {
      this.socket.emit('new:chat', chat, (chatObj) => {
        console.log('[] New chat created: ', chatObj)
        let obj = {}
        obj[chatObj.chatId] = chatObj
        this.chats = Object.assign({}, this.chats, obj)
      })
      this.show_new_chat = false
    },
    newUser (user) {
      this.socket.emit('new:user', user)
    },
    openChat (chatId) {
      this.currentChat = chatId
    },
    getAllChats () {
      this.socket.emit('get:all:chats', (m) => {
        let chats = m
        console.log('[] Got chats: ', chats)
        for (let chatId in chats) {
          let hasNew = false
          let chat = chats[chatId]
          chat.newMessages = false
          for (let msg of chat.messages) {
            if (!msg.read.includes(this.currentUser)) {
              chat.newMessages = true
              hasNew = true
              break
            }
          }
          if (hasNew) {
          } else {
          }
        }
        this.chats = Object.assign({}, this.chats, JSON.parse(JSON.stringify(chats)))
      })
    },
    login (id) {
      this.socket.emit('login', id, () => {
        this.currentUser = id
        console.log('[] User log in: ', id)
        this.getAllChats()
      })
    },
    sendMessage (chatId, message) {
      console.log('[] Message to be sent to chat ', chatId, ' : ', message)
      let messageObj = {
        user: this.currentUser,
        content: message
      }
      this.chats[chatId].messages.push(messageObj)
      let index = this.chats[chatId].messages.length - 1
      this.socket.emit('send:message', {
        chatId: chatId,
        content: message
      }, (msjObj) => {
        // messageObj = Object.assign({}, messageObj, msjObj)
        console.log('[] Acknowledge message sent: ', msjObj)
        let chats = JSON.parse(JSON.stringify(this.chats))
        chats[chatId].messages[index] = msjObj
        this.chats = Object.assign({}, this.chats, JSON.parse(JSON.stringify(chats)))
      })
    }
  },
  mounted () {
    this.socket.on('new:message', (m) => {
      console.log('[] Received new message: ', m)
      this.chats[m.chatId].messages.push(m.message)
      this.chats[m.chatId].newMessages = true
    })
    this.socket.on('ack:read', (m) => {
      console.log('[] Marking as read: ', m)
      let chats = JSON.parse(JSON.stringify(this.chats))
      for (let msgRead of m.messagesRead) {
        for (let i = 0; i < chats[m.chatId].messages.length; i++) {
          if (chats[m.chatId].messages[i].messageId === msgRead.messageId) {
            chats[m.chatId].messages[i].read = msgRead.read
          }
        }
      }
      chats[m.chatId].newMessages = false
      this.chats = Object.assign({}, this.chats, JSON.parse(JSON.stringify(chats)))
    })
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
    border: none;
    padding: 0.5em;
    background-color: #e7ffa3;
  }

  .side {
    background-color: #333333;
    padding: 1em;
    width: 30%;
  }

  .chat-view {
    width: 70%;
  }

  .current-chat {
    background-color: red;
  }

  button:hover {
    background-color: #95a66a;
  }

  button:active {
    box-shadow: none;
  }

  .chat-item {
    background-color: #e7ffa3;
    margin-bottom: 1em;
    padding: 0.5em;
    color: black;
  }

  .chat-item-new {
    background-color: yellow;
  }

  .current-chat {
    border: solid 2px green;
  }

  .other-chat {
    border: none;
  }

  .new-chat {
    margin-bottom: 1em;
  }

  input[type="text"] {
    border: none;
    padding: 0.5em;
  }
</style>
