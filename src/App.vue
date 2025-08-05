<script setup>
import { computed, onBeforeMount } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const screenWidth = computed(() => window.screen.width)
const visibleHeader = computed(
  () =>
    router.currentRoute.value.path !== '/login' || router.currentRoute.value.path !== '/register',
)

onBeforeMount(async () => {
  if (!localStorage.getItem('token')) {
    router.push('/login')
  }
  if (screenWidth.value < 1200) {
    router.push('/dev')
  }
})
</script>

<template>
  <header v-if="visibleHeader" class="flex items-center maw-w-[1200px] w-full shadow-xl bg-white">
    <ul class="flex justify-end gap-3 px-5 py-2 h-[40px]">
      <li class="text-[#609aff] text-sm cursor-pointer">Профиль</li>
      <li class="text-[#609aff] text-sm cursor-pointer">Пользователи</li>
    </ul>
  </header>
  <router-view></router-view>
</template>
