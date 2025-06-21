<template>
  <label class="inline-flex items-center cursor-pointer">
    <!-- Hidden checkbox -->
    <input type="checkbox" class="sr-only" :checked="isDark" @change="$emit('toggle')" />

    <!-- Track -->
    <div
      :class="[
        'w-12 h-7 rounded-full relative transition-colors duration-300',
        isDark ? 'bg-gray-700' : 'bg-gray-300'
      ]"
    >

      <!-- Knob -->
      <div
        class="absolute left-1 top-1 bg-white w-5 h-5 rounded-full shadow transform transition-transform duration-300"
        :class="{ 'translate-x-5': isDark }"
      ></div>
    </div>

    <!-- Label -->
    <span
      :class="[
        'ml-3 text-sm transition-colors duration-300',
        isDark ? 'text-gray-100' : 'text-gray-600'
      ]"
    >
      {{ isDark ? 'Dark' : 'Light' }}
    </span>
  </label>
</template>

<script setup lang="ts">
defineProps<{ isDark: boolean }>()
defineEmits(['toggle'])
</script>
