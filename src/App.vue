<template>
  <v-app :theme="currentTheme">
    <v-btn 
      @click="toggleTheme" 
      variant="tonal"
      class="theme-btn"
    >
      {{ isDark ? '☀️' : '🌙' }}
    </v-btn>

    <v-container class="fill-height d-flex flex-column align-center">
      <v-card class="pa-4 my-4" width="600">
        <v-card-title class="text-h5 text-center">📝 Мои заметки</v-card-title>
      </v-card>

      <v-text-field
        v-model="searchQuery"
        label="🔍 Что-то ищешь?"
        variant="outlined"
        class="search-box"
      ></v-text-field>

      <note-form @add-note="addNote" />
      <v-btn @click="toggleSortOrder" color="primary" class="mt-4">
        {{ isDescending ? 'Сортировать от старых к новым' : 'Сортировать от новых к старым' }}
      </v-btn>
      <note-list :notes="filteredNotes" @delete-note="deleteNote" class="mt-4" />
    </v-container>
  </v-app>
</template>

<style>
.theme-btn {
  position: fixed;
  top: 15px;
  right: 15px;
  z-index: 1000;
}
.search-box {
  width: 600px;
  max-width: 90%;
}
</style>

<script lang="ts">
import { defineComponent, ref, watch, computed } from 'vue';
import NoteForm from './components/NoteForm.vue';
import NoteList from './components/NoteList.vue';
import { useTheme } from 'vuetify';

export default defineComponent({
  components: {
    NoteForm,
    NoteList
  },
  setup() {
    const notes = ref<Array<{ text: string; category: string; date: string }>>(
      JSON.parse(localStorage.getItem('notes') || '[]')
    );
    const searchQuery = ref<string>('');
    const theme = useTheme();

    const isDark = ref<boolean>(
      localStorage.getItem('theme') === 'dark' ||
      (!localStorage.getItem('theme') && window.matchMedia('(prefers-color-scheme: dark)').matches)
    );

    const currentTheme = computed(() => (isDark.value ? 'dark' : 'light'));

    const toggleTheme = () => {
      isDark.value = !isDark.value;
      localStorage.setItem('theme', isDark.value ? 'dark' : 'light');
      theme.global.name.value = currentTheme.value;
    };

    watch(isDark, (newVal) => {
      theme.global.name.value = newVal ? 'dark' : 'light';
    });

    const isDescending = ref(true);

    const toggleSortOrder = () => {
      isDescending.value = !isDescending.value;
    };

    const filteredNotes = computed(() => {
      return notes.value
        .filter(note =>
          note.text.toLowerCase().includes(searchQuery.value.toLowerCase()) // фильтрация по ключевому слову
        )
        .sort((a, b) => 
          isDescending.value
            ? new Date(b.date).getTime() - new Date(a.date).getTime()  // от новых к старым
            : new Date(a.date).getTime() - new Date(b.date).getTime()  // от старых к новым
        );
    });

    const addNote = (note: { text: string; category: string; date: string }) => {
      notes.value.push(note);
    };

    const deleteNote = (index: number) => {
      notes.value.splice(index, 1);
    };

    watch(notes, (newNotes) => {
      localStorage.setItem('notes', JSON.stringify(newNotes));
    }, { deep: true });

    return {
      notes,
      searchQuery,
      filteredNotes,
      addNote,
      deleteNote,
      toggleTheme,
      isDark,
      currentTheme,
      toggleSortOrder,
      isDescending,
    };
  }
});
</script>