<template>
  <div class="container">
    <div
      class="box"
      :style="{
        left: position.x + 'px',
        top: position.y + 'px',
      }"
    ></div>
    <p>Наклон телефона:</p>
    <p>Beta (X): {{ orientation.beta?.toFixed(2) || 'Нет данных' }}</p>
    <p>Gamma (Y): {{ orientation.gamma?.toFixed(2) || 'Нет данных' }}</p>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue'

export default {
  setup() {
    const orientation = ref({
      beta: null, // Наклон вперед/назад (X-ось)
      gamma: null, // Наклон влево/вправо (Y-ось)
    })

    const position = ref({
      x: 0,
      y: 0,
    })

    const maxTilt = 30 // Максимальный угол наклона (градусы)
    const speed = 2 // Скорость движения

    const handleOrientation = (event) => {
      orientation.value.beta = event.beta // Наклон вперед/назад (-180 до 180)
      orientation.value.gamma = event.gamma // Наклон влево/вправо (-90 до 90)

      // Ограничиваем угол наклона
      const tiltX = Math.min(Math.max(event.gamma, -maxTilt), maxTilt)
      const tiltY = Math.min(Math.max(event.beta, -maxTilt), maxTilt)

      // Двигаем блок
      position.value.x += tiltX * speed
      position.value.y += tiltY * speed

      // Ограничиваем позицию, чтобы блок не уходил за экран
      const maxX = window.innerWidth - 50
      const maxY = window.innerHeight - 50

      position.value.x = Math.min(Math.max(position.value.x, 0), maxX)
      position.value.y = Math.min(Math.max(position.value.y, 0), maxY)
    }

    onMounted(() => {
      window.addEventListener('deviceorientation', handleOrientation)
    })

    onUnmounted(() => {
      window.removeEventListener('deviceorientation', handleOrientation)
    })

    return {
      orientation,
      position,
    }
  },
}
</script>

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

p {
  margin: 10px;
  font-family: Arial, sans-serif;
}
</style>
