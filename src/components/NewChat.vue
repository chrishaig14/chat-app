<template>
  <div id="new-chat">
    <div class="chat-form">
      <div class="chat-user" v-for="user in chat.users" :key="user">
        <span class="user">{{user}}</span>
        <button @click="deleteUser(user)">-</button>
      </div>
      <form @submit.prevent="handleAddUser">
        <input type="text" v-model="newUser">
        <button>+</button>
      </form>
    </div>
    <label>Name:
      <!--      <input type="text" onfocus="this.placeholder=''" ref="name"-->

      <input type="text" ref="name"
             :placeholder="chat.users.join(',')"
             v-model="chat.name"/>
    </label>
    <button @click="createChat">Create chat</button>
    <div :class="ok?'msg-ok':'msg-not-ok'" v-if="msg!== ''">{{msg}}</div>
  </div>
</template>|

<script>
export default {
  name: 'NewChat',
  data () {
    return {chat: {name: '', users: []}, newUser: '', msg: '', ok: false}
  },
  methods: {
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

      if (this.chat.users.length === 0) {
        // console.log('IS NOT OK!')
        this.msg = 'You must add at least one user to the chat!'
        this.ok = false
      } else {
        if (this.chat.name === '') {
          this.chat.name = this.chat.users.join(',')
        }
        this.msg = `Chat "${this.chat.name}" created ok`
        this.ok = true
      }
      this.$emit('create:chat', this.chat)
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

  .chat-form {
    margin-bottom: 1em;
  }
</style>
