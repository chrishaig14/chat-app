<template>
  <div id="signup">
    <form @submit.prevent="handleSubmit">
      <label for="username">Username</label>
      <input id="username" type="text" v-model="user">

      <button>Signup</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Signup',
  data () {
    return {
      user: ''
    }
  },
  methods: {
    handleSubmit () {
      this.$emit('new:user', this.user)
    }
  }
}
</script>

<style scoped>
  label {
    padding-right: 1em;
  }
</style>
