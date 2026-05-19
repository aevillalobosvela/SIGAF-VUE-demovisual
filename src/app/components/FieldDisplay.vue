<template>
  <div :class="className">
    <label class="block text-xs text-gray-600 mb-1">{{ label }}</label>
    <div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-900">
      {{ value }}
    </div>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  label: string
  value: string
  className?: string
}>()
</script>
