<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add-book'])

const newBook = ref({
  title: '',
  author: '',
  genre: '',
  photo: '',
  description: '',
})

const handleSubmit = () => {
  if (!newBook.value.title.trim() || !newBook.value.author.trim()) {
    alert('Заполните название и автора')
    return
  }
  emit('add-book', {
    title: newBook.value.title.trim(),
    author: newBook.value.author.trim(),
    genre: newBook.value.genre || 'Другое',
    photo: newBook.value.photo.trim(),
    description: newBook.value.description.trim(),
  })
  newBook.value = { title: '', author: '', genre: '', photo: '', description: '' }
}
</script>

<template>
  <div class="card shadow-sm mb-4">
    <div class="card-body">
      <h5 class="card-title mb-3">Добавить книгу</h5>
      <div class="row g-2">
        <div class="col-md-4">
          <input v-model="newBook.title" type="text" class="form-control"
                 placeholder="Название книги" />
        </div>
        <div class="col-md-3">
          <input v-model="newBook.author" type="text" class="form-control"
                 placeholder="Автор" />
        </div>
        <div class="col-md-2">
          <select v-model="newBook.genre" class="form-select">
            <option value="">Жанр</option>
            <option>Роман</option>
            <option>Фантастика</option>
            <option>Детектив</option>
            <option>Научная</option>
            <option>Поэзия</option>
            <option>Биография</option>
          </select>
        </div>
        <div class="col-md-3">
          <input v-model="newBook.photo" type="url" class="form-control"
                 placeholder="URL обложки (необязательно)" />
        </div>
        <div class="col-12">
          <textarea v-model="newBook.description" class="form-control"
                    rows="2" placeholder="Описание (необязательно)"></textarea>
        </div>
        <div class="col-12">
          <button class="btn btn-primary w-100" @click="handleSubmit">
            + Добавить в коллекцию
          </button>
        </div>
      </div>
    </div>
  </div>
</template>