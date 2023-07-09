<script setup lang="ts">
import VPagination from '@hennge/vue3-pagination'
import '@hennge/vue3-pagination/dist/vue3-pagination.css'
import { onMounted, reactive, ref } from 'vue'

interface User {
  id: number
  email: string
  first_name: string
  last_name: string
  avatar: string
}

const state = reactive<{
  users: User[]
}>({
  users: []
})

const currentPage = ref(1)

const fetchData = async () => {
  try {
    let response
    if (currentPage.value === 1) {
      response = await fetch('https://reqres.in/api/users')
    } else if (currentPage.value === 2) {
      response = await fetch('https://reqres.in/api/users?page=2')
    }

    if (response) {
      const data = await response.json()
      state.users = data.data
    }
  } catch (error) {
    console.error(error)
  }
}

onMounted(fetchData)

const updateHandler = (page: number) => {
  currentPage.value = page
  fetchData()
}
</script>

<template>
  <div class="flex marginTop">
    <div class="person-container marginBottom" v-for="user in state.users" :key="user.id">
      <img class="round" :src="user.avatar" :alt="`${user.first_name} ${user.last_name}`" />
      <h3>{{ user.first_name }} {{ user.last_name }}</h3>
      <p><a :href="`mailto:${user.email}`">Contact</a></p>
    </div>
  </div>
  <div class="pageBar">
    <v-pagination
      v-model="currentPage"
      :pages="2"
      :range-size="1"
      active-color="#DCEDFF"
      :hide-first-button="true"
      :hide-last-button="true"
      @update:modelValue="updateHandler"
    />
  </div>
</template>

<style>
.flex {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 0.75rem;
}

.marginTop {
  margin-top: 5rem;
}

.person-container {
  background-color: rgb(242, 242, 242);
  width: auto;
  height: auto;
  align-items: center;
  text-align: center;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
  padding-bottom: 0.2rem;
}

.round {
  border-radius: 50%;
  width: 150px;
  height: 150px;
  padding: 1rem;
}

.pageBar {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 1rem;
}
</style>
