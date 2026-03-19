<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import AddBookForm  from './components/AddBookForm.vue'
import BookFilters  from './components/BookFilters.vue'
import BookCard     from './components/BookCard.vue'

// ── Состояние ──────────────────────────────────────────────
const books         = ref([])
const currentFilter = ref('all')
const searchQuery   = ref('')
const sortOrder     = ref('newest')

// ── localStorage ───────────────────────────────────────────
onMounted(() => {
  try {
    const saved = localStorage.getItem('books-p15')
    if (saved) books.value = JSON.parse(saved)
  } catch { /* пустой список */ }
})

watch(books, (val) => {
  localStorage.setItem('books-p15', JSON.stringify(val))
}, { deep: true })

// ── Методы ─────────────────────────────────────────────────
const addBook = (bookData) => {
  books.value.push({
    id: Date.now(),
    ...bookData,
    completed: false,
    favorite: false,
    rating: 0,
  })
}

const toggleBook = (id) => {
  const b = books.value.find(b => b.id === id)
  if (b) { b.completed = !b.completed; if (!b.completed) b.rating = 0 }
}

const favoriteBook = (id) => {
  const b = books.value.find(b => b.id === id)
  if (b) b.favorite = !b.favorite
}

const rateBook = ({ id, rating }) => {
  const b = books.value.find(b => b.id === id)
  if (b && b.completed) b.rating = rating
}

const deleteBook = (id) => {
  if (confirm('Удалить книгу?'))
    books.value = books.value.filter(b => b.id !== id)
}

// ── Фильтрация и сортировка (computed) ─────────────────────
const sortedBooks = computed(() => {
  let list = books.value
    .filter(book => {
      if (currentFilter.value === 'unread')   return !book.completed
      if (currentFilter.value === 'read')     return  book.completed
      if (currentFilter.value === 'favorite') return  book.favorite
      return true
    })
    .filter(book => {
      if (!searchQuery.value.trim()) return true
      const q = searchQuery.value.toLowerCase()
      return book.title.toLowerCase().includes(q) ||
             book.author.toLowerCase().includes(q)
    })

  if (sortOrder.value === 'newest') return [...list].reverse()
  if (sortOrder.value === 'oldest') return list
  if (sortOrder.value === 'az')     return [...list].sort((a, b) => a.title.localeCompare(b.title, 'ru'))
  if (sortOrder.value === 'rating') return [...list].sort((a, b) => b.rating - a.rating)
  return list
})
</script>

<template>
  <div>
    <!-- Шапка -->
    <div class="hero mb-4">
      <div class="container text-center">
        <h1 class="display-5 fw-bold">📚 Моя коллекция</h1>
        <p class="text-white-50 mb-0">Управляй своими книгами</p>
      </div>
    </div>

    <div class="container pb-5">

      <!-- Форма добавления -->
      <AddBookForm @add-book="addBook" />

      <!-- Фильтры, поиск, сортировка, статистика -->
      <BookFilters
        v-model:filter="currentFilter"
        v-model:search="searchQuery"
        v-model:sortOrder="sortOrder"
        :books="books"
      />

      <!-- Пустое состояние -->
      <div v-if="sortedBooks.length === 0" class="empty-state text-center text-muted">
        <div style="font-size:64px">📖</div>
        <h4 class="mt-3">
          {{ books.length === 0 ? 'Коллекция пуста' : 'Ничего не найдено' }}
        </h4>
        <p>
          {{ books.length === 0
            ? 'Добавьте первую книгу выше!'
            : 'Попробуйте изменить фильтры или поиск' }}
        </p>
      </div>

      <!-- Карточки книг -->
      <div v-else class="row g-3">
        <BookCard
          v-for="book in sortedBooks"
          :key="book.id"
          :book="book"
          @toggle="toggleBook"
          @delete="deleteBook"
          @favorite="favoriteBook"
          @rate="rateBook"
        />
      </div>

    </div>
  </div>
</template>

<style>
body {
  background: #f0f2f5;
}
.hero {
  background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
  color: white;
  padding: 40px 0 30px;
}
.empty-state {
  padding: 60px 20px;
}
</style>