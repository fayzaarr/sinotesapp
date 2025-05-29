<template>
  <div :class="[isDark ? 'bg-gray-900 text-white' : 'bg-white text-gray-900']" class="min-h-screen flex flex-col p-6 transition-colors duration-300">
    
    <!-- Konten utama: flex-grow supaya footer tetap di bawah -->
    <div class="flex-grow">
      <!-- Header -->
      <div class="text-center mb-6">
        <h1 class="text-2xl font-bold">Si.Notes App</h1>
      </div>

      <!-- Search and Add New -->
      <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4 mb-4">
        <div class="relative flex-1">
          <span class="absolute left-3 top-2.5 text-gray-400 dark:text-gray-500 pointer-events-none">🔍</span>
          <input
            v-model="searchQuery"
            placeholder="Search notes..."
            class="pl-10 pr-4 py-2 w-full border rounded-md focus:outline-none focus:ring-2 focus:ring-gray-700
                  placeholder-gray-400 dark:placeholder-gray-500 dark:bg-gray-800 dark:text-white"
          />
        </div>

        <button
          class="border px-4 py-2 rounded-md hover:bg-gray-500 dark:hover:bg-gray-700 flex items-center gap-2"
          @click="openModal"
        >
          ➕ Add new
        </button>
      </div>

      <!-- Filter Tags & button toggle theme -->
      <div class="flex justify-between">
        <div class="flex flex-wrap gap-2 mb-6">
          <button
            v-for="tag in ['Study', 'Personal', 'Work', 'All']"
            :key="tag"
            :class="[
              'border rounded-full px-4 py-1 transition',
              selectedTag === tag
                ? 'bg-gray-950 text-white'
                : 'hover:bg-gray-600 dark:hover:bg-gray-700'
            ]"
            @click="selectTag(tag)"
          >
            #{{ tag }}
          </button>
        </div>

        <div class="flex justify-end mb-4">
          <ThemeToggle :isDark="isDark" @toggle="isDark = !isDark" />
        </div>
      </div>

      <!-- Notes List -->
      <div class="flex flex-wrap gap-4">
        <div
          v-for="(note, index) in filteredNotes"
          :key="index"
          class="w-70 rounded-md border p-4 shadow-sm transition-colors duration-300"
        >
          <!-- Header: Title + Actions -->
          <div class="flex justify-between items-start mb-3">
            <div class="flex items-center gap-2">
              <input type="checkbox" class="w-4 h-4" />
              <h2 class="font-semibold text-lg">{{ note.title }}</h2>
            </div>
            <div class="flex gap-2 text-lg">
              <button @click="editNote(index)" class="hover:scale-110 cursor-pointer transition-transform duration-200">✏️</button>
              <button @click="deleteNote(index)" class="hover:scale-110 cursor-pointer transition-transform duration-200">🗑️</button>
              <button @click="downloadSingleNote(note)" class="hover:scale-110 cursor-pointer transition-transform duration-200">📥</button>
            </div>
          </div>

          <!-- Content with format -->
          <div v-if="note.format === 'Plain'" class="mb-3 border rounded p-2 whitespace-pre-wrap">
            {{ note.content }}
          </div>

          <ul v-else-if="note.format === 'Bullets'" class="mb-3 border rounded p-2 list-disc list-inside whitespace-pre-wrap">
            <li v-for="(line, i) in note.content.split('\n')" :key="i">{{ line }}</li>
          </ul>

          <ol v-else-if="note.format === 'Numbers'" class="mb-3 border rounded p-2 list-decimal list-inside whitespace-pre-wrap">
            <li v-for="(line, i) in note.content.split('\n')" :key="i">{{ line }}</li>
          </ol>

          <!-- Footer inside note card -->
          <div class="text-sm text-gray-500 dark:text-gray-500">
            {{ formatDate() }} –
            <span class="font-medium text-gray-500 dark:text-gray-500">
              #{{ note.tag || 'General' }}
            </span>
            <div class="italic">Format: {{ note.format }}</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Footer umum aplikasi -->
    <footer class="mt-6 text-center text-sm text-gray-500 dark:text-gray-400">
      © 2025 Si.Notes App • Made with ❤️
    </footer>

    <!-- Modal Note Form -->
    <div
      v-if="showModal"
      class="fixed inset-0 bg-black/30 backdrop-blur-md bg-opacity-50 flex items-center justify-center z-50"
      @click.self="closeModal"
    >
      <div class="border border-white bg-white dark:bg-gray-900 p-6 rounded-md w-full max-w-md">
        <button
          class="mb-4 text-gray-600 dark:text-gray-300 hover:text-gray-900 dark:hover:text-white"
          @click="closeModal"
        >
          ✖ Close
        </button>
        <NoteForm
          v-model="newNote"
          :isEditing="editIndex !== null"
          @submit="submitNote"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue'
import NoteForm from './components/NoteForm.vue'
import ThemeToggle from './components/ThemeToggle.vue'

type NoteFormat = 'Plain' | 'Bullets' | 'Numbers'

type Note = {
  title: string
  content: string
  tag?: string
  format: NoteFormat
}

const notes = ref<Note[]>([])

const newNote = ref<Note>({
  title: '',
  content: '',
  tag: '',
  format: 'Plain'
})

const searchQuery = ref('')
const isDark = ref(false)
const editIndex = ref<number | null>(null)
const selectedTag = ref('All')
const showModal = ref(false)

function openModal() {
  showModal.value = true
  editIndex.value = null
  newNote.value = {
    title: '',
    content: '',
    tag: '',
    format: 'Plain'
  }
}

function closeModal() {
  showModal.value = false
}

function submitNote() {
  addNote()
  closeModal()
}

function addNote() {
  if (!newNote.value.title || !newNote.value.content) return

  if (editIndex.value !== null) {
    notes.value[editIndex.value] = { ...newNote.value }
    editIndex.value = null
  } else {
    notes.value.push({ ...newNote.value })
  }

  newNote.value = {
    title: '',
    content: '',
    tag: '',
    format: 'Plain'
  }
}

function deleteNote(index: number) {
  notes.value.splice(index, 1)
}

function editNote(index: number) {
  const note = notes.value[index]
  newNote.value = {
    title: note.title,
    content: note.content,
    tag: note.tag || '',
    format: note.format || 'Plain'
  }
  editIndex.value = index
  showModal.value = true
}

function downloadSingleNote(note: Note) {
  const content = `Judul: ${note.title}\nIsi: ${note.content}\nTag: ${note.tag || 'General'}\nFormat: ${note.format}\nTanggal: ${formatDate()}`
  const blob = new Blob([content], { type: 'text/plain' })
  const url = URL.createObjectURL(blob)

  const a = document.createElement('a')
  a.href = url
  a.download = `${note.title || 'catatan'}.txt`
  a.click()

  URL.revokeObjectURL(url)
}

function selectTag(tag: string) {
  selectedTag.value = tag
}

function formatDate() {
  const now = new Date()
  return now.toLocaleDateString('en-GB', { day: '2-digit', month: 'short', year: 'numeric' })
}

const filteredNotes = computed(() => {
  const query = searchQuery.value.toLowerCase()
  return notes.value.filter(note => {
    const matchesQuery =
      note.title.toLowerCase().includes(query) ||
      note.content.toLowerCase().includes(query) ||
      (note.tag?.toLowerCase().includes(query) ?? false)

    const matchesTag = selectedTag.value === 'All' || note.tag === selectedTag.value

    return matchesQuery && matchesTag
  })
})

onMounted(() => {
  const savedNotes = localStorage.getItem('smart-notes')
  const savedTheme = localStorage.getItem('theme')
  if (savedNotes) notes.value = JSON.parse(savedNotes)
  if (savedTheme === 'dark') isDark.value = true
})

watch(notes, (newVal) => {
  localStorage.setItem('smart-notes', JSON.stringify(newVal))
}, { deep: true })

watch(isDark, (val) => {
  document.documentElement.classList.toggle('dark', val)
  localStorage.setItem('theme', val ? 'dark' : 'light')
})
</script>

<style scoped>
input[type="checkbox"] {
  width: 16px;
  height: 16px;
}
</style>
