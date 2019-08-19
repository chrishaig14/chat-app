<template>
  <div id="new-chat">
    <button @click="()=>{chat.type='simple'; resetChat();}">Simple</button>
    <button @click="()=>{chat.type='group'; resetChat();}">Group</button>
    <div v-if="chat.type==='simple'" class="simple-chat-form">
      <label>User:</label>
      <div class="input-div" onblur="clicked" contenteditable="true">
        <input type="text" v-model="newUser"
               @focus="suggesting=true">
        <div v-if="suggesting" class="suggest-box">
          <div :key="sug" v-for="sug in suggestions" class="suggest-item"
               @click="selectSuggestion(sug)">{{sug}}
          </div>
        </div>
      </div>
    </div>
    <div v-if="chat.type==='group'" class="group-chat-form">
      <div class="chat-user" v-for="user in chat.users" :key="user">
        <span class="user">{{user}}</span>
        <button @click="deleteUser(user)">-</button>
      </div>
      <form @submit.prevent="handleAddUser">
        <input type="text" v-model="newUser">
        <button>+</button>
      </form>
      <div class="chat-name">
        <label>Name:
          <input type="text" ref="name"
                 :placeholder="chat.users.join(',')"
                 v-model="chat.name"/>
        </label>
      </div>
    </div>
    <button @click="createChat">Create chat</button>
    <button @click="$emit('cancel')">Cancel</button>
    <div :class="ok?'msg-ok':'msg-not-ok'" v-if="msg!== ''">{{msg}}</div>
  </div>
</template>

<script>
export default {
  name: 'NewChat',
  data () {
    return {
      chat: {type: 'simple', name: '', users: []},
      newUser: '',
      msg: '',
      ok: false,
      suggesting: true,
      pepe: ''
    }
  },
  props: {
    suggestions: Array
  },
  watch: {
    newUser: function (val) {
      if (val !== '') {
        this.$emit('suggest:users', val)
      }
    },
    suggestions: (val) => {
    }
  },
  methods: {
    logsug () {
    },
    clicked () {
    },
    selectSuggestion (sug) {
      this.newUser = sug
      this.suggesting = false
    },
    resetChat () {
      // this.chat.type = ''
      this.chat.name = ''
      this.chat.users = []
      this.newUser = ''
    },
    resetName () {
      if (this.chat.name === '') {
        this.$refs['name'].placeholder = this.chat.users.join(',')
      }
    },
    deleteUser (user) {
      this.chat.users = this.chat.users.filter(u => u !== user)
    },
    handleAddUser () {
      if (this.newUser === '') {
        return
      }
      this.chat.users.push(this.newUser)
      this.newUser = ''
    },
    createChat () {
      if (this.chat.type === 'simple') {
        if (this.newUser === '') {
          this.msg = 'You must add a user to the chat!'
          this.ok = false
          return
        }
        this.msg = `Chat with "${this.newUser}" created ok`
        this.ok = true
        this.chat.users.push(this.newUser)
      }
      if (this.chat.type === 'group') {
        if (this.chat.users.length === 0) {
          this.msg = 'You must add at least one user to the chat!'
          this.ok = false
          return
        }
        if (this.chat.name === '') {
          this.chat.name = this.chat.users.join(',')
        }
        this.msg = `Chat "${this.chat.name}" created ok`
        this.ok = true
      }

      this.$emit('new:chat', this.chat)
      this.resetChat()
    }
  }
}
</script>

<style scoped>
  .chat-user {
    margin-bottom: 1em;
    border: solid 1px white;
    display: flex;
    flex-direction: row;
    align-items: center;
  }

  .user {
    flex-grow: 1;
  }

  form {
    display: flex;
    flex-direction: row;
  }

  form input {
    flex-grow: 1
  }

  .msg-ok {
    background-color: green;
  }

  .msg-not-ok {
    background-color: orangered;
  }

  .group-chat-form {
    margin-bottom: 1em;
    padding: 1em;
  }

  .simple-chat-form {
    margin-bottom: 1em;
    padding: 1em;
    display: flex;
    flex-direction: row;
  }

  .input-div {
    position: relative;
    flex-grow: 1;
  }

  .input-div input {
    width: 100%;
  }

  .suggest-box {
    position: absolute;
    background-color: white;
    width: 100%;
    display: flex;
    flex-direction: column;
  }

  .suggest-item {
    color: black;
    padding: 1em;
  }

  .suggest-item:hover {
    background-color: lightgreen;
  }

  .chat-name {
    display: flex;
    margin-top: 1em;
    flex-direction: row;
  }

  .chat-name label {
    flex-grow: 1;
  }
</style>
