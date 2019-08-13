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
        <new-chat :suggestions="suggestions" @suggest:users='suggestUsers'
                  class="new-chat"
                  @new:chat="newChat"></new-chat>
        <div @click="openChat(chat)" class="chat-item"
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
      currentUser: '',
      messages: [],
      msgs: {},
      chats: {},
      currentChat: '',
      socket: io('localhost:3000'),
      newMessages: {},
      suggestions: []
    }
  },
  methods: {
    suggestUsers (str) {
      this.socket.emit('suggest:users', str, (result) => {
        console.log('SUGGESTIONS: ', result)
        this.suggestions = result

      })
    },
    markRead (data) {
      this.socket.emit('mark:read', data)
    },
    newChat (chat) {
      console.log('NEW CHAT: ', chat)
      this.socket.emit('new:chat', chat)
    },
    newUser (user) {
      console.log('ADDED aNEW USER')
      this.socket.emit('new:user', user)
    },
    openChat (chatId) {
      console.log('OPENING CHAT: ', chatId)
      this.currentChat = chatId
      // this.socket.emit('get:chat', chatId)
      // this.chats[chatId].newMessages = false
    },
    login (id) {
      this.socket.emit('login', id, this.callAfterLogin)
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
    },
    callAfterLogin () {
      console.log('THIS WAS CALLED!')
    }
  },
  mounted () {
    this.socket.on('login:ok', () => {
      console.log('LOGGED IN OK')
      // this.socket.emit('get:contacts',)
      this.socket.emit('get:all:chats')
    })
    this.socket.on('login:error', () => {
      console.log('THERE WAS AN ERROR LOGGING IN')
    })
    this.socket.on('all:chats', (m) => {
      console.log('RECEIVED CHATSaaa: ', m.chats)
      let chats = m.chats
      for (let chatId in chats) {
        console.log('processing chat #', chatId)
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
          console.log('new messages in chat')
        } else {
          console.log('no new messages in chat')
        }
      }
      console.log('CHATS::: ', m.chats)
      this.chats = Object.assign({}, this.chats, JSON.parse(JSON.stringify(chats)))
    })
    this.socket.on('chat', (m) => {
      console.log('on chat', m)
      console.log('ALL CHATS ARE: ', this.chats)
      m.messages = m.messages.map(msg => ({
        ...msg, ack: true
      }))
      this.chats[m.chatId].messages = m.messages
      this.chats[m.chatId].newMessages = true
      console.log('ALL CHATS ARE: ', this.chats)
    })
    this.socket.on('new:message', (m) => {
      console.log('on new:message', m)
      this.chats[m.chatId].messages.push(m.message)
      this.chats[m.chatId].newMessages = true
    })
    this.socket.on('ack:message', (m) => {
      console.log('on ack:message')
      // let msg = this.msgs[m]
      console.log('RECEIVED ACK FOR MESSAGE: ', m)

      // this.chats[this.currentChat].messages.push({
      //   user: this.currentUser,
      //   content: m,
      //   ack: false,
      //   sqn: sqn
      // })

      // for (let i = 0; i < this.chats[m.chatId].messages.length; i++) {
      //   let msg = this.chats[m.chatId].messages[i]
      //   if (msg.sqn === m.sqn) {
      //     this.chats[m.chatId].messages[i] = m.message
      //   }
      // }

      // this.chats[m.chatId].messages.push(m.message)

      let chats = JSON.parse(JSON.stringify(this.chats))
      // for (let msgRead of m.messagesRead) {
      for (let i = 0; i < chats[m.chatId].messages.length; i++) {
        if (chats[m.chatId].messages[i].sqn === m.sqn) {
          chats[m.chatId].messages[i] = m.message
        }
      }
      // }

      // this.chats = Object.assign({},this)
      this.chats = Object.assign({}, this.chats, JSON.parse(JSON.stringify(chats)))

    })
    this.socket.on('ack:read', (m) => {
      console.log('RECEIVED MESSAGE READ INFORMATION!', m)
      let chats = JSON.parse(JSON.stringify(this.chats))
      for (let msgRead of m.messagesRead) {
        for (let i = 0; i < chats[m.chatId].messages.length; i++) {
          if (chats[m.chatId].messages[i].messageId === msgRead.messageId) {
            chats[m.chatId].messages[i].read = msgRead.read
          }
        }
      }

      // this.chats = Object.assign({},this)
      this.chats = Object.assign({}, this.chats, JSON.parse(JSON.stringify(chats)))
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
    width: 30%;
  }

  .chat-view {
    /*flex-grow: 1*/
    width: 70%;
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
    /*border-radius: 1.5rem;*/
    border: none;
    padding: 0.5em;
    /*font-size: 14pt;*/
    /*border: solid 1px gray;*/
  }
</style>
