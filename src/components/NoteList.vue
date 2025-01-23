<template>
    <v-container>
      <v-row v-if="notes.length">
        <v-col v-for="(note, index) in notes" :key="index" cols="12" md="6">
          <v-card class="pa-3">
            <v-card-title>
              <v-text-field
                v-if="note.isEditing"
                v-model="note.text"
                variant="outlined"
                density="compact"
                hide-details
                class="flex-grow-1"
              ></v-text-field>
              <span v-else>{{ note.text }}</span>
            </v-card-title>
            <v-card-subtitle>
              📂 {{ note.category }} • 📅 {{ new Date(note.date).toLocaleString() }}
            </v-card-subtitle>
            <v-card-actions>
              <v-btn v-if="note.isEditing" color="success" @click="saveEdit(index)">Сохранить</v-btn>
              <v-btn v-else color="primary" @click="editNote(index)">Редактировать</v-btn>
              <v-btn color="red" @click="$emit('delete-note', index)">Удалить</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
  
      <v-row v-else>
        <v-col cols="12" class="text-center">
          <v-alert type="info" variant="outlined">
            📌 Заметок нет
          </v-alert>
        </v-col>
      </v-row>
    </v-container>
  </template>
  
  <script lang="ts">
  import { defineComponent } from 'vue';
  
  export default defineComponent({
    props: {
      notes: Array as () => { text: string; category: string; date: string; isEditing?: boolean }[],
    },
    emits: ['delete-note', 'update-note'],
    setup(props, { emit }) {
      const editNote = (index: number) => {
        props.notes[index].isEditing = true;
      };
  
      const saveEdit = (index: number) => {
        props.notes[index].isEditing = false;
        emit('update-note', index, props.notes[index].text);
      };
  
      return { editNote, saveEdit };
    }
  });
  </script>
  