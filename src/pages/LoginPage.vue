<script setup>
import axios from 'axios'
import { useRouter } from 'vue-router'
import { computed, ref } from 'vue'
import LoginInput from '@/components/ui/LoginInput.vue'

const router = useRouter()
const form = ref({
  email: '',
  password: '',
})
const isButtonDisabled = computed(() => !form.value.email || !form.value.password)

const login = async () => {
  try {
    if (!form.value.email || !form.value.password) {
      return
    }
    const { data } = await axios.post('https://a16b7f641c9d755d.mokky.dev/auth', {
      email: form.value.email,
      password: form.value.password,
    })
    localStorage.setItem('token', data.token)
    router.push('/')
  } catch (error) {
    console.log(error)
    if (error.status === 401) {
      router.push('/register')
    }
  }
}
</script>

<template>
  <div class="flex flex-col items-center bg-white p-10 rounded-xl shadow-xl w-full max-w-[400px]">
    <h1 class="text-2xl font-bold">Выпить?</h1>
    <p class="mt-3">Добро пожаловать в мир алкоголиков!</p>
    <form @submit.prevent="login" class="mt-5 w-full flex flex-col items-center gap-3">
      <LoginInput
        v-model="form.email"
        :id="'email'"
        placeholder="ivan@mail.ru"
        label="Почта"
        type="email"
      />
      <LoginInput
        v-model="form.password"
        :id="'password'"
        placeholder="*********"
        label="Пароль"
        type="password"
      />
      <button
        type="submit"
        :disabled="isButtonDisabled"
        class="w-full bg-[#609aff] mt-5 px-4 py-2 text-white rounded transition cursor-pointer hover:opacity-70 disabled:opacity-50 disabled:cursor-not-allowed"
      >
        Войти
      </button>
      <p class="text-xs">
        Еще не зарегистрированы?
        <router-link
          to="/register"
          class="text-[#609aff] underline transition cursor-pointer hover:opacity-70"
          >Зарегистрироваться</router-link
        >
      </p>
    </form>
  </div>
</template>
