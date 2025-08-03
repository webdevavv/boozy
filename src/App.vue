<script setup>
import axios from 'axios'
import { onBeforeMount } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

onBeforeMount(async () => {
  try {
    await axios.get('https://a16b7f641c9d755d.mokky.dev/auth_me')
  } catch (error) {
    console.log(error)
    if (error.response.status === 401) {
      router.push('/login')
    } else {
      router.push('/register')
    }
  }
})
</script>

<template>
  <div class="wrapper">
    <router-view></router-view>
  </div>
</template>

<style scoped>
.wrapper {
  height: 100dvh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #609aff;
}
</style>
