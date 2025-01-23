<template>
    <v-card class="pa-4" width="600">
      <v-form @submit.prevent="submitNote">
        <v-text-field
          v-model="noteText"
          label="✏️ Напишите заметку..."
          variant="outlined"
          required
        ></v-text-field>
  
        <!-- Добавляем выбор категории -->
        <v-select
          v-model="selectedCategory"
          :items="categories"
          label="📂 Категория"
          variant="outlined"
          required
        ></v-select>
  
        <v-btn type="submit" color="primary" block>Добавить</v-btn>
      </v-form>
    </v-card>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref } from 'vue';
  
  export default defineComponent({
    emits: ['add-note'],
    setup(_, { emit }) {
      const noteText = ref('');
      const selectedCategory = ref('Личное'); // Категория по умолчанию
      const categories = ['Работа', 'Учеба', 'Личное']; // Доступные категории
  
      const submitNote = () => {
        if (!noteText.value.trim()) return;
  
        emit('add-note', {
          text: noteText.value,
          category: selectedCategory.value,
          date: new Date().toISOString() // Добавляем дату создания
        });
  
        noteText.value = '';
        selectedCategory.value = 'Личное'; // Сбрасываем выбор категории
      };
  
      return { noteText, selectedCategory, categories, submitNote };
    }
  });
  </script>
  