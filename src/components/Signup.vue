<template>
  <div id="signup">
    <form @submit.prevent="handleSubmit">
      <label>Username<input type="text" v-model="user"></label>

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

</style>
