<template>
  <div id="new-chat">
    <button @click="()=>{chat.type='simple'; resetChat();}">Simple</button>
    <button @click="()=>{chat.type='group'; resetChat();}">Group</button>
    <div v-if="chat.type==='simple'" class="simple-chat-form">
      <label>User: <input type="text" v-model="newUser"></label>
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
      ok: false
    }
  },
  methods: {
    resetChat () {
      // this.chat.type = ''
      this.chat.name = ''
      this.chat.users = []
      this.newUser = ''
      console.log('RESETTING CHAT')
    },
    resetName () {
      if (this.chat.name === '') {
        this.$refs['name'].placeholder = this.chat.users.join(',')
      }
    },
    deleteUser (user) {
      this.chat.users = this.chat.users.filter(u => u !== user)
      console.log('DELETING USER:', this.chat.users)
    },
    handleAddUser () {
      if (this.newUser === '') {
        return
      }
      this.chat.users.push(this.newUser)
      this.newUser = ''
    },
    createChat () {
      console.log('CREATNIG CHAT: ', this.chat.users)
      // console.log(this.$data.chat.users)
      // console.log('this.msg:', this.msg)
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

      console.log('CREATING CHAT: ', this.chat)
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
