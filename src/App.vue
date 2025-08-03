<script setup>
import { computed, onBeforeMount } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const visibleHeader = computed(
  () =>
    router.currentRoute.value.path !== '/login' || router.currentRoute.value.path !== '/register',
)

onBeforeMount(async () => {
  if (!localStorage.getItem('token')) {
    router.push('/login')
  }
})
</script>

<template>
  <header v-if="visibleHeader" class="px-5 py-2 rounded bg-whiteh-[30px] flex items-center">
    <ul class="flex gap-3">
      <li class="text-[#609aff] text-md">Профиль</li>
      <li class="text-[#609aff] text-md">Пользователи</li>
    </ul>
  </header>
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
