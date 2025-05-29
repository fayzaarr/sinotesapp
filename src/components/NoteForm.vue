<template>
  <form
    @submit.prevent="handleSubmit"
    class="mb-6 space-y-3 bg-white dark:bg-gray-800 text-black dark:text-white p-6 rounded shadow-md transition-colors duration-300"
  >
    <!-- Input Judul -->
    <input
      v-model="localNote.title"
      type="text"
      placeholder="Judul"
      class="w-full p-2 border rounded bg-white dark:bg-gray-700 
             border-gray-300 dark:border-gray-600 
             text-black dark:text-white 
             placeholder-gray-400 dark:placeholder-gray-500"
    />

    <!-- Tombol Format -->
    <div class="flex gap-2">
      <button
        v-for="option in formatOptions"
        :key="option"
        type="button"
        @click="localNote.format = option"
        :class="[ 
          'px-3 py-1 rounded border transition',
          localNote.format === option
            ? 'bg-gray-700 text-white'
            : 'bg-gray-100 dark:bg-gray-700 hover:bg-gray-200 dark:hover:bg-gray-600'
        ]"
      >
        {{ option }}
      </button>
    </div>

    <!-- Textarea Isi -->
    <textarea
      v-model="localNote.content"
      placeholder="Isi catatan"
      class="w-full p-2 resize-y border rounded bg-white dark:bg-gray-700 
             border-gray-300 dark:border-gray-600 
             text-black dark:text-white 
             placeholder-gray-400 dark:placeholder-gray-500"
      rows="5"
    />

    <!-- Input Tag -->
    <input
      v-model="localNote.tag"
      type="text"
      placeholder="Tag (opsional)"
      class="w-full p-2 border rounded dark:bg-gray-800 dark:border-gray-600 dark:text-white placeholder-gray-400 dark:placeholder-gray-500"
    />

    <!-- Tombol Submit -->
    <button
      type="submit"
      :disabled="!localNote.title || !localNote.content"
      class="w-full px-4 py-2 rounded font-semibold transition 
             bg-gray-500 text-white hover:bg-gray-900 
             disabled:bg-gray-200 disabled:text-gray-500 
             dark:disabled:bg-gray-700 dark:disabled:text-gray-400"
    >
      {{ isEditing ? 'Update' : 'Tambah' }} Catatan
    </button>
  </form>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

// ✅ Tipe untuk format catatan
type NoteFormat = 'Plain' | 'Bullets' | 'Numbers'

// ✅ Tipe untuk satu catatan
interface Note {
  title: string
  content: string
  tag?: string
  format: NoteFormat
}

// Props dari parent
const props = defineProps<{
  modelValue: Note
  isEditing: boolean
}>()

const emit = defineEmits(['submit', 'update:modelValue'])

const formatOptions: NoteFormat[] = ['Plain', 'Bullets', 'Numbers']

// Local copy dari note, inisialisasi dengan default format
const localNote = ref<Note>({
  ...props.modelValue,
  format: props.modelValue.format || 'Plain'
})

// Sync props ke localNote
watch(() => props.modelValue, (newVal) => {
  localNote.value = {
    ...newVal,
    format: newVal.format || 'Plain'
  }
})

// Sync localNote ke parent lewat emit
watch(localNote, (newVal) => {
  emit('update:modelValue', newVal)
}, { deep: true })

function handleSubmit() {
  emit('submit')
}
</script>
