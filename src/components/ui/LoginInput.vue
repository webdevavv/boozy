<script setup>
const model = defineModel()
defineProps({
  label: String,
  type: {
    type: String,
    default: 'text',
  },
  placeholder: String,
  id: String,
})
</script>
<template>
  <div class="input_block flex flex-col gap-1 w-full">
    <label v-if="label" :for="id" class="text-xs -mb-1">{{ label }}</label>
    <input
      class="p-2 border border-slate-300 rounded focus:border-slate-500"
      :type="type"
      :id="id"
      :placeholder="placeholder"
      v-model="model"
    />
  </div>
</template>
