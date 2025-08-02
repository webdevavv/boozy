<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// Константы
const MAX_TILT = 30
const SPEED = 1
const BOX_SIZE = 50
const BOX_COUNT = 5
const BOX_COLORS = ['#42b983', '#647eff', '#ff9f43', '#ff5e7d', '#9067ff']

// Состояния
const orientation = ref({
  beta: null,
  gamma: null,
})

const boxes = ref(
  Array(BOX_COUNT)
    .fill()
    .map((_, i) => ({
      id: i,
      x: Math.random() * (window.innerWidth - BOX_SIZE),
      y: Math.random() * (window.innerHeight - BOX_SIZE),
      color: BOX_COLORS[i % BOX_COLORS.length],
    })),
)

const isPermissionGranted = ref(false)
const isIOS = ref(false)

// Проверка столкновений
const checkCollision = (box1, box2) => {
  return (
    box1.x < box2.x + BOX_SIZE &&
    box1.x + BOX_SIZE > box2.x &&
    box1.y < box2.y + BOX_SIZE &&
    box1.y + BOX_SIZE > box2.y
  )
}

// Обработчик ориентации
const handleOrientation = (event) => {
  if (!isPermissionGranted.value) return

  orientation.value = {
    beta: event.beta,
    gamma: event.gamma,
  }

  const tiltX = Math.min(Math.max(event.gamma, -MAX_TILT), MAX_TILT)
  const tiltY = Math.min(Math.max(event.beta, -MAX_TILT), MAX_TILT)

  // Обновляем позиции всех элементов
  boxes.value.forEach((box) => {
    const newX = box.x + tiltX * SPEED
    const newY = box.y + tiltY * SPEED

    // Проверяем границы экрана
    const maxX = window.innerWidth - BOX_SIZE
    const maxY = window.innerHeight - BOX_SIZE

    box.x = Math.max(0, Math.min(newX, maxX))
    box.y = Math.max(0, Math.min(newY, maxY))
  })

  // Обработка столкновений
  for (let i = 0; i < boxes.value.length; i++) {
    for (let j = i + 1; j < boxes.value.length; j++) {
      if (checkCollision(boxes.value[i], boxes.value[j])) {
        // Простое отталкивание
        const dx = boxes.value[i].x - boxes.value[j].x
        const dy = boxes.value[i].y - boxes.value[j].y

        boxes.value[i].x += dx * 0.1
        boxes.value[i].y += dy * 0.1
        boxes.value[j].x -= dx * 0.1
        boxes.value[j].y -= dy * 0.1
      }
    }
  }
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
    isPermissionGranted.value = true
    window.addEventListener('deviceorientation', handleOrientation)
  }
}

// Инициализация
onMounted(() => {
  isIOS.value = /iPad|iPhone|iPod/.test(navigator.userAgent)

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
      v-for="box in boxes"
      :key="box.id"
      class="box"
      :style="{
        left: box.x + 'px',
        top: box.y + 'px',
        backgroundColor: box.color,
      }"
    >
      {{ box.id + 1 }}
    </div>

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
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
  font-size: 18px;
  transition: transform 0.1s ease;
  user-select: none;
}

.debug-info {
  position: absolute;
  bottom: 20px;
  left: 20px;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 10px;
  border-radius: 5px;
  font-family: Arial, sans-serif;
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
