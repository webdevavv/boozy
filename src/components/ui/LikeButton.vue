<script setup>
import HeartIcon from '../icons/HeartIcon.vue'
defineProps({
  isLiked: Boolean,
})
</script>

<template>
  <HeartIcon :color="isLiked ? '#ff6060' : '#609aff'" />
</template>
