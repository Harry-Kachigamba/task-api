<template>
    <div>
      <h1>Tasks</h1>
      <button @click="logout">Logout</button>

      <form @submit.prevent="createTask">
        <input v-model="newTask.title" placeholder="Title">
        <input v-model="newTask.description" placeholder="Description">
        <button type="submit">Add Task</button>
      </form>

      <ul>
        <li v-for="task in tasks" :key="task.id">
          {{ task.title }} - {{ task.description }}
          <button @click="deleteTask(task.id)">Delete</button>
          <button @click="editTask(task)">Edit</button>
        </li>
      </ul>

      <div v-if="editingTask">
        <h3>Edit Task</h3>
        <input v-model="editingTask.title">
        <input v-model="editingTask.description">
        <button @click="updateTask">Update</button>
        <button @click="cancelEdit">Cancel</button>
      </div>
    </div>
  </template>

  <script>
  import api from '../api'

  export default {
    data() {
      return {
        tasks: [],
        newTask: { title: '', description: '' },
        editingTask: null
      }
    },
    async created() {
      await this.fetchTasks()
    },
    methods: {
      async fetchTasks() {
        try {
          const { data } = await api.get('/tasks')
          this.tasks = data
        } catch (error) {
          alert('Error fetching tasks')
        }
      },
      async createTask() {
        try {
          const { data } = await api.post('/tasks', this.newTask)
          this.tasks.push(data)
          this.newTask = { title: '', description: '' }
        } catch (error) {
          alert('Error creating task')
        }
      },
      async deleteTask(id) {
        try {
          await api.delete(`/tasks/${id}`)
          this.tasks = this.tasks.filter(task => task.id !== id)
        } catch (error) {
          alert('Error deleting task')
        }
      },
      editTask(task) {
        this.editingTask = { ...task }
      },
      async updateTask() {
        try {
          const { data } = await api.put(`/tasks/${this.editingTask.id}`, this.editingTask)
          this.tasks = this.tasks.map(task =>
            task.id === data.id ? data : task
          )
          this.cancelEdit()
        } catch (error) {
          alert('Error updating task')
        }
      },
      cancelEdit() {
        this.editingTask = null
      },
      async logout() {
        try {
          await api.post('/logout')
          localStorage.removeItem('token')
          this.$router.push('/login')
        } catch (error) {
          alert('Logout failed')
        }
      }
    }
  }
  </script>
