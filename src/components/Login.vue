<template>
  <div id="login">
    <form @submit.prevent="handleSubmit">
      <label for="username">Username</label>
      <input autocomplete="off" id="username" type="text" v-model="user">

      <button>Login</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data () {
    return {
      user: ''
    }
  },
  methods: {
    handleSubmit () {
      this.$emit('login', this.user)
    }
  }
}
</script>

<style scoped>
  label {
    padding-right: 1em;
  }
</style>
