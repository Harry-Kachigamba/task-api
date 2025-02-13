<template>
    <form @submit.prevent="login">
      <input v-model="form.email" type="email" placeholder="Email">
      <input v-model="form.password" type="password" placeholder="Password">
      <button type="submit">Login</button>
    </form>
  </template>

  <script>
  import api from '../api'

  export default {
    data() {
      return {
        form: {
          email: '',
          password: ''
        }
      }
    },
    methods: {
      async login() {
        try {
          const { data } = await api.post('/login', this.form)
          localStorage.setItem('token', data.token)
          this.$router.push('/tasks')
        } catch (error) {
          alert('Login failed')
        }
      }
    }
  }
  </script>
