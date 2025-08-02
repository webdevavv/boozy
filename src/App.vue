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
      x: getRandomPosition(window.innerWidth),
      y: getRandomPosition(window.innerHeight),
      color: BOX_COLORS[i % BOX_COLORS.length],
    })),
)

const isPermissionGranted = ref(false)
const isIOS = ref(false)

// Генерация случайной позиции с учетом границ
function getRandomPosition(max) {
  return Math.random() * (max - BOX_SIZE * 2) + BOX_SIZE
}

// Проверка столкновений с более точной физикой
function resolveCollisions() {
  for (let i = 0; i < boxes.value.length; i++) {
    for (let j = i + 1; j < boxes.value.length; j++) {
      const box1 = boxes.value[i]
      const box2 = boxes.value[j]

      // Вектор между центрами
      const dx = box1.x + BOX_SIZE / 2 - (box2.x + BOX_SIZE / 2)
      const dy = box1.y + BOX_SIZE / 2 - (box2.y + BOX_SIZE / 2)
      const distance = Math.sqrt(dx * dx + dy * dy)

      // Минимальное расстояние без столкновения
      const minDistance = BOX_SIZE

      if (distance < minDistance) {
        // Угол столкновения
        const angle = Math.atan2(dy, dx)
        // Глубина проникновения
        const overlap = minDistance - distance

        // Корректировка позиций
        const moveX = (overlap * Math.cos(angle)) / 2
        const moveY = (overlap * Math.sin(angle)) / 2

        box1.x += moveX
        box1.y += moveY
        box2.x -= moveX
        box2.y -= moveY

        // Гарантируем, что блоки остаются в пределах экрана
        constrainToScreen(box1)
        constrainToScreen(box2)
      }
    }
  }
}

// Удерживаем блоки в пределах экрана
function constrainToScreen(box) {
  box.x = Math.max(0, Math.min(box.x, window.innerWidth - BOX_SIZE))
  box.y = Math.max(0, Math.min(box.y, window.innerHeight - BOX_SIZE))
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

  // Двигаем все блоки
  boxes.value.forEach((box) => {
    box.x += tiltX * SPEED
    box.y += tiltY * SPEED
    constrainToScreen(box)
  })

  // Разрешаем столкновения
  resolveCollisions()
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

  // Проверяем начальные столкновения
  resolveCollisions()

  if (!isIOS.value || typeof DeviceOrientationEvent.requestPermission !== 'function') {
    requestPermission()
  }

  // Обработка ресайза
  window.addEventListener('resize', () => {
    boxes.value.forEach((box) => constrainToScreen(box))
    resolveCollisions()
  })
})

// Очистка
onUnmounted(() => {
  window.removeEventListener('deviceorientation', handleOrientation)
  window.removeEventListener('resize', () => {})
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
        transform: `rotate(${orientation.gamma || 0}deg)`,
      }"
    >
      {{ box.id + 1 }}
    </div>

    <div class="debug-info">
      <p>
        Наклон: X: {{ orientation.beta?.toFixed(1) || '-' }}° Y:
        {{ orientation.gamma?.toFixed(1) || '-' }}°
      </p>
    </div>

    <button v-if="isIOS && !isPermissionGranted" @click="requestPermission" class="permission-btn">
      Разрешить доступ к датчикам
    </button>
  </div>
</template>

<style scoped>
.container {
  position: relative;
  width: calc(100vw - 20px);
  margin: auto auto;
  height: calc(100vw - 20px);
  overflow: hidden;
  background: #f0f0f0;
}

.box {
  position: absolute;
  width: 50px;
  height: 50px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
  font-size: 18px;
  transition:
    left 0.1s ease-out,
    top 0.1s ease-out,
    transform 0.1s ease;
  user-select: none;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16);
  will-change: transform;
}

.debug-info {
  position: absolute;
  bottom: 20px;
  left: 20px;
  right: 20px;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 10px 15px;
  border-radius: 8px;
  font-family: Arial, sans-serif;
  text-align: center;
  font-size: 14px;
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
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  z-index: 10;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
</style>
