<template>
  <form
    @submit.prevent="handleSubmit"
    class="mb-6 space-y-3 p-6 rounded shadow-md transition-colors duration-300"
    :class="isDark ? 'bg-gray-800 text-white' : 'bg-white text-black'"
  >
    <!-- Input Judul -->
    <input
      v-model="localNote.title"
      type="text"
      placeholder="Judul"
      :class="[
        'w-full p-2 border rounded placeholder-gray-400 transition-colors duration-300',
        isDark
          ? 'bg-gray-700 border-gray-600 text-white placeholder-gray-500'
          : 'bg-white border-gray-300 text-black placeholder-gray-400'
      ]"
    />

    <!-- Tombol Format -->
    <div class="flex gap-2">
      <button
        v-for="option in formatOptions"
        :key="option"
        type="button"
        @click="localNote.format = option"
        :class="[
          'px-3 py-1 rounded border transition-colors duration-200',
          localNote.format === option
            ? (isDark ? 'bg-gray-700 text-white border-gray-500' : 'bg-gray-300 text-black border-gray-400')
            : (isDark ? 'bg-gray-600 text-white hover:bg-gray-500 border-gray-500' : 'bg-gray-100 text-black hover:bg-gray-200 border-gray-300')
        ]"
      >
        {{ option }}
      </button>
    </div>

    <!-- Textarea Isi -->
    <textarea
      v-model="localNote.content"
      placeholder="Isi catatan"
      rows="5"
      :class="[
        'w-full p-2 resize-y border rounded transition-colors duration-300',
        isDark
          ? 'bg-gray-700 border-gray-600 text-white placeholder-gray-500'
          : 'bg-white border-gray-300 text-black placeholder-gray-400'
      ]"
    />

    <!-- Input Tag -->
    <input
      v-model="localNote.tag"
      type="text"
      placeholder="Tag (opsional)"
      :class="[
        'w-full p-2 border rounded transition-colors duration-300',
        isDark
          ? 'bg-gray-700 border-gray-600 text-white placeholder-gray-500'
          : 'bg-white border-gray-300 text-black placeholder-gray-400'
      ]"
    />

    <!-- Tombol Submit -->
    <button
      type="submit"
      :disabled="!localNote.title || !localNote.content"
      :class="[
        'w-full px-4 py-2 rounded font-semibold transition-colors duration-300',
        (!localNote.title || !localNote.content)
          ? (isDark ? 'bg-gray-700 text-gray-400' : 'bg-gray-200 text-gray-500')
          : (isDark ? 'bg-gray-600 text-white hover:bg-gray-500' : 'bg-gray-500 text-white hover:bg-gray-700')
      ]"
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
  isDark: boolean
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
