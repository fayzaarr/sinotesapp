<template>
  <label class="inline-flex items-center cursor-pointer">
    <!-- Hidden checkbox untuk trigger -->
    <input type="checkbox" class="sr-only" :checked="isDark" @change="$emit('toggle')" />

    <!-- Track -->
    <div
      class="w-12 h-7 bg-gray-400 dark:bg-gray-900 rounded-full relative transition-colors duration-300"
    >
      <!-- Knob -->
      <div
        class="absolute left-1 top-1 bg-white w-5 h-5 rounded-full shadow transform transition-transform duration-300"
        :class="{ 'translate-x-5': isDark }"
      ></div>
    </div>

    <!-- Label -->
    <span class="ml-3 text-sm text-gray-600 dark:text-gray-100">
      {{ isDark ? 'Dark' : 'Light' }}
    </span>
  </label>
</template>

<script setup lang="ts">
defineProps<{ isDark: boolean }>()
defineEmits(['toggle'])
</script>
