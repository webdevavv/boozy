<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// Состояния
const orientation = ref({
  beta: null,
  gamma: null,
})

const position = ref({
  x: window.innerWidth / 2 - 25,
  y: window.innerHeight / 2 - 25,
})

const isPermissionGranted = ref(false)
const isIOS = ref(false)

// Константы
const MAX_TILT = 30
const SPEED = 2
const BOX_SIZE = 50

// Обработчик ориентации
const handleOrientation = (event) => {
  if (!isPermissionGranted.value) return

  orientation.value = {
    beta: event.beta,
    gamma: event.gamma,
  }

  // Рассчитываем движение с ограничениями
  const tiltX = Math.min(Math.max(event.gamma, -MAX_TILT), MAX_TILT)
  const tiltY = Math.min(Math.max(event.beta, -MAX_TILT), MAX_TILT)

  position.value.x += tiltX * SPEED
  position.value.y += tiltY * SPEED

  // Границы экрана
  const maxX = window.innerWidth - BOX_SIZE
  const maxY = window.innerHeight - BOX_SIZE

  position.value.x = Math.max(0, Math.min(position.value.x, maxX))
  position.value.y = Math.max(0, Math.min(position.value.y, maxY))
}

// Запрос разрешения для iOS
const requestPermission = () => {
  if (
    typeof DeviceOrientationEvent !== 'undefined' &&
    typeof DeviceOrientationEvent.requestPermission === 'function'
  ) {
    DeviceOrientationEvent.requestPermission()
      .then((response) => {
        if (response === 'granted') {
          isPermissionGranted.value = true
          window.addEventListener('deviceorientation', handleOrientation)
        }
      })
      .catch(console.error)
  } else {
    // Для Android и других браузеров
    isPermissionGranted.value = true
    window.addEventListener('deviceorientation', handleOrientation)
  }
}

// Инициализация
onMounted(() => {
  isIOS.value = /iPad|iPhone|iPod/.test(navigator.userAgent)

  // Для iOS <13 и других устройств
  if (!isIOS.value || typeof DeviceOrientationEvent.requestPermission !== 'function') {
    requestPermission()
  }
})

// Очистка
onUnmounted(() => {
  window.removeEventListener('deviceorientation', handleOrientation)
})
</script>

<template>
  <div class="container">
    <div
      class="box"
      :style="{
        left: position.x + 'px',
        top: position.y + 'px',
      }"
    ></div>

    <div class="debug-info">
      <p>Наклон телефона:</p>
      <p>Beta (X): {{ orientation.beta?.toFixed(2) || 'Нет данных' }}</p>
      <p>Gamma (Y): {{ orientation.gamma?.toFixed(2) || 'Нет данных' }}</p>
    </div>

    <button v-if="isIOS && !isPermissionGranted" @click="requestPermission" class="permission-btn">
      Разрешить доступ к датчикам
    </button>
  </div>
</template>

<style scoped>
.container {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: #f0f0f0;
}

.box {
  position: absolute;
  width: 50px;
  height: 50px;
  background: #42b983;
  border-radius: 8px;
  transition: transform 0.1s ease;
}

.debug-info {
  position: absolute;
  bottom: 20px;
  left: 20px;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 10px;
  border-radius: 5px;
}

.permission-btn {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 15px 25px;
  background: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  z-index: 10;
}
</style>
