<template>
  <v-container class="fill-height" fluid>
    <v-row justify="center" align="center">
      <v-col cols="12" md="6" class="text-center">
        <div v-if="loading">检测登录状态...</div>
        <div v-else>
          <div v-if="user">
            <h2>欢迎，{{ user.username }}</h2>
            <p>角色: {{ user.role }}</p>
            <v-btn color="primary" @click="logout">退出登录</v-btn>
          </div>
          <div v-else>
            <h2>你尚未登录</h2>
            <v-btn color="primary" @click="goLogin">去登录</v-btn>
          </div>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from '@/plugins/axios'

interface User {
  id: number
  username: string
  role: string
}

const router = useRouter()
const user = ref<User | null>(null)
const loading = ref(true)

async function checkLogin() {
  try {
    const res = await axios.get('/user/me')
    if (res.data.code === 0) {
      user.value = res.data.data
    } else {
      user.value = null
    }
  } catch {
    user.value = null
  } finally {
    loading.value = false
  }
}

async function logout() {
  await axios.post('/user/logout')
  document.cookie = 'token=; path=/; expires=Thu, 01 Jan 1970 00:00:00 GMT'
  user.value = null
}

function goLogin() {
  router.push('/login')
}

onMounted(() => {
  checkLogin()
})
</script>

<style scoped>
h2 {
  margin-bottom: 16px;
}
</style>
