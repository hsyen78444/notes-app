<script setup>
import { ref, onMounted, computed } from 'vue';

// ==========================================
// 1. STATE MANAGEMENT
// ==========================================
const notes = ref([]);
const isModalOpen = ref(false);
const isLoading = ref(false);
const errorMsg = ref('');

// Form State
const currentNote = ref({
  id: null,
  title: '',
  content: ''
});

// ==========================================
// 2. API CONFIGURATION (TODO: UPDATE THIS)
// ==========================================
const API_BASE_URL = 'http://localhost:8080/api/notes'; // Replace with your actual deployed Backend URL

// ==========================================
// 3. CRUD OPERATIONS
// ==========================================

// READ: Fetch all notes
const fetchNotes = async () => {
  isLoading.value = true;
  try {
    const response = await fetch(API_BASE_URL);
    const data = await response.json();
    notes.value = data;
  } catch (err) {
    errorMsg.value = 'Failed to load notes.';
    console.error(err);
  } finally {
    isLoading.value = false;
  }
};

// CREATE & UPDATE: Handle Form Submit
const saveNote = async () => {
  if (!currentNote.value.title || !currentNote.value.content) return;

  const isEditing = !!currentNote.value.id;
  const method = isEditing ? 'PUT' : 'POST';
  const url = isEditing 
    ? `${API_BASE_URL}/${currentNote.value.id}` 
    : API_BASE_URL;

  try {
    
    const response = await fetch(url, {
      method: method,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ 
        title: currentNote.value.title, 
        content: currentNote.value.content 
      })
    });
    if (!response.ok) throw new Error('API Error');
    closeModal();
    await fetchNotes(); // Refresh list from server
  } catch (err) {
    console.error(err);
    alert('Error saving note');
  }
};

// DELETE: Remove a note
const deleteNote = async (id) => {
  const confirmDelete = confirm("Are you sure you want to delete this note?");
  if (!confirmDelete) return;

  try {
    const response = await fetch(`${API_BASE_URL}/${id}`, { method: 'DELETE' });
    if (!response.ok) throw new Error('Delete failed');
    await fetchNotes(); // Refresh list from server
  } catch (err) {
    console.error(err);
    alert('Failed to delete');
  }
};

// ==========================================
// 4. UI HELPERS
// ==========================================
const openModal = (note = null) => {
  if (note) {
    currentNote.value = { ...note };
  } else {
    currentNote.value = { id: null, title: '', content: '' };
  }
  isModalOpen.value = true;
};

const closeModal = () => {
  isModalOpen.value = false;
  errorMsg.value = '';
};

// Lifecycle Hook
onMounted(() => {
  fetchNotes();
});
</script>

<template>
  <div class="min-h-screen p-6 md:p-12 text-gray-800">
    
    <header class="max-w-5xl mx-auto flex justify-between items-center mb-10">
      <div>
        <h1 class="text-3xl font-bold tracking-tight text-gray-900">Notes</h1>
        <p class="text-gray-500 mt-1 text-sm">Manage your thoughts.</p>
      </div>
      <button 
        @click="openModal()" 
        class="bg-black hover:bg-gray-800 text-white px-5 py-2.5 rounded-full font-medium transition-all transform hover:scale-105 shadow-lg active:scale-95 flex items-center gap-2"
      >
        <span>+ New Note</span>
      </button>
    </header>

    <div v-if="isLoading" class="text-center py-20 text-gray-400">
      Loading...
    </div>

    <div v-else class="max-w-5xl mx-auto grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      
      <div 
        v-for="note in notes" 
        :key="note.id" 
        class="group bg-white rounded-2xl p-6 shadow-sm border border-gray-100 hover:shadow-xl hover:-translate-y-1 transition-all duration-300 relative overflow-hidden cursor-pointer"
        @click="openModal(note)"
      >
        <h2 class="text-xl font-semibold mb-2 text-gray-900 pr-8 truncate">{{ note.title }}</h2>
        <p class="text-gray-500 text-sm leading-relaxed line-clamp-3">{{ note.content }}</p>
        
        <div class="mt-6 text-xs text-gray-300 font-medium">Just now</div>

        <div class="absolute top-4 right-4 opacity-0 group-hover:opacity-100 transition-opacity flex gap-2">
          <button 
            @click.stop="deleteNote(note.id)"
            class="p-2 bg-red-50 hover:bg-red-100 text-red-600 rounded-full transition-colors"
            title="Delete Note"
          >
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
              <path stroke-linecap="round" stroke-linejoin="round" d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0" />
            </svg>
          </button>
        </div>
      </div>

      <div v-if="notes.length === 0" class="col-span-full text-center py-20">
        <p class="text-gray-400">No notes yet. Create your first one!</p>
      </div>

    </div>

    <div v-if="isModalOpen" class="fixed inset-0 z-50 flex items-center justify-center p-4">
      <div class="absolute inset-0 bg-gray-900/20 backdrop-blur-sm transition-opacity" @click="closeModal"></div>

      <div class="bg-white rounded-2xl shadow-2xl w-full max-w-lg relative overflow-hidden transform transition-all animate-fade-in-up">
        
        <div class="px-6 py-4 border-b border-gray-100 flex justify-between items-center bg-gray-50/50">
          <h3 class="font-semibold text-gray-900">
            {{ currentNote.id ? 'Edit Note' : 'New Note' }}
          </h3>
          <button @click="closeModal" class="text-gray-400 hover:text-gray-600">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <div class="p-6">
          <input 
            v-model="currentNote.title"
            placeholder="Title" 
            class="w-full text-2xl font-bold text-gray-900 placeholder-gray-300 border-none focus:ring-0 p-0 mb-4 bg-transparent"
            autofocus
          />
          <textarea 
            v-model="currentNote.content"
            placeholder="Type your note here..." 
            class="w-full h-40 text-gray-600 placeholder-gray-300 border-none focus:ring-0 p-0 resize-none bg-transparent text-lg leading-relaxed"
          ></textarea>
        </div>

        <div class="px-6 py-4 bg-gray-50 flex justify-end gap-3">
          <button @click="closeModal" class="px-4 py-2 text-gray-500 font-medium hover:bg-gray-200 rounded-lg transition-colors">Cancel</button>
          <button 
            @click="saveNote" 
            :disabled="!currentNote.title || !currentNote.content"
            class="px-6 py-2 bg-black text-white rounded-lg font-medium shadow-md hover:shadow-lg disabled:opacity-50 disabled:cursor-not-allowed transition-all"
          >
            Save
          </button>
        </div>
      </div>
    </div>

  </div>
</template>

<style scoped>
/* Simple animation for the modal entrance */
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(10px) scale(0.98); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}
.animate-fade-in-up {
  animation: fadeInUp 0.2s ease-out forwards;
}
</style>