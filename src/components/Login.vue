<template>
  <div id="login">
    <form @submit.prevent="handleSubmit">
      <label>Username<input type="text" v-model="user"></label>

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

</style>
