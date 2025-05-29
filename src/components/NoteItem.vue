<template>
  <li class="mb-4 p-4 border rounded bg-white dark:bg-gray-800 shadow">
    <!-- Judul -->
    <h2 class="text-lg font-bold text-gray-900 dark:text-white">{{ note.title }}</h2>

    <!-- Konten berdasarkan format -->
    <div v-if="note.format === 'Plain'" class="text-sm text-gray-700 dark:text-gray-300 whitespace-pre-wrap border rounded p-2 mt-2">
      {{ note.content }}
    </div>

    <ul v-else-if="note.format === 'Bullets'" class="list-disc list-inside text-sm text-gray-700 dark:text-gray-300 border rounded p-2 mt-2 whitespace-pre-wrap">
      <li v-for="(line, i) in note.content.split('\n')" :key="i">{{ line }}</li>
    </ul>

    <ol v-else-if="note.format === 'Numbers'" class="list-decimal list-inside text-sm text-gray-700 dark:text-gray-300 border rounded p-2 mt-2 whitespace-pre-wrap">
      <li v-for="(line, i) in note.content.split('\n')" :key="i">{{ line }}</li>
    </ol>

    <!-- Tag -->
    <span v-if="note.tag" class="inline-block mt-2 bg-blue-200 text-blue-800 text-xs px-2 py-1 rounded mr-1">
      #{{ note.tag }}
    </span>

    <!-- Format Label -->
    <div class="text-xs italic text-gray-500 mt-1">
      Format: {{ note.format }}
    </div>

    <!-- Tombol Aksi -->
    <div class="mt-3 flex gap-3">
      <button
        @click="$emit('edit', index)"
        class="text-sm text-yellow-600 hover:underline font-medium"
      >
        Edit
      </button>
      <button
        @click="$emit('delete', index)"
        class="text-sm text-red-600 hover:underline font-medium"
      >
        Hapus
      </button>
    </div>
  </li>
</template>

<script setup lang="ts">
defineProps<{
  note: {
    title: string
    content: string
    tag?: string
    format: 'Plain' | 'Bullets' | 'Numbers'
  }
  index: number
}>()

defineEmits(['edit', 'delete'])
</script>
