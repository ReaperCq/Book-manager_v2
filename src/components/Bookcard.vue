<script setup>
defineProps(['book'])
defineEmits(['toggle', 'delete', 'favorite', 'rate'])

const genreEmoji = (genre) => {
  const map = {
    'Роман': '📖', 'Фантастика': '🚀', 'Детектив': '🔍',
    'Научная': '🔬', 'Поэзия': '🎭', 'Биография': '👤',
  }
  return map[genre] || '📚'
}
</script>

<template>
  <div class="col-md-4 col-sm-6">
    <div class="card h-100 shadow-sm">

      <!-- Обложка / заглушка -->
      <img
        v-if="book.photo"
        :src="book.photo"
        class="card-img-top"
        :alt="book.title"
        @error="book.photo = ''"
      />
      <div v-else class="img-placeholder">
        {{ genreEmoji(book.genre) }}
      </div>

      <div class="card-body d-flex flex-column">

        <!-- Бейджи -->
        <div class="d-flex gap-1 mb-2 flex-wrap">
          <span class="badge bg-secondary badge-genre">{{ book.genre || 'Другое' }}</span>
          <span v-if="book.completed" class="badge bg-success completed-badge">✓ Прочитано</span>
          <span v-if="book.favorite"  class="badge bg-warning text-dark completed-badge">★ Избранное</span>
        </div>

        <h5 class="card-title">{{ book.title }}</h5>
        <p class="text-muted small mb-1">{{ book.author }}</p>
        <p
          v-if="book.description"
          class="card-text small text-muted flex-grow-1"
          style="overflow:hidden; display:-webkit-box; -webkit-line-clamp:2; -webkit-box-orient:vertical"
        >{{ book.description }}</p>

        <!-- Рейтинг звёздами (только для прочитанных) -->
        <div v-if="book.completed" class="star-rating my-2">
          <span
            v-for="star in 5" :key="star"
            @click="$emit('rate', { id: book.id, rating: star })"
          >{{ star <= book.rating ? '★' : '☆' }}</span>
          <small class="text-muted ms-1" v-if="book.rating">({{ book.rating }}/5)</small>
        </div>

        <!-- Кнопки действий -->
        <div class="d-flex gap-1 mt-auto pt-2 flex-wrap">
          <button
            class="btn btn-sm btn-outline-warning"
            :title="book.favorite ? 'Убрать из избранного' : 'В избранное'"
            @click="$emit('favorite', book.id)"
          >{{ book.favorite ? '★' : '☆' }}</button>

          <button
            :class="['btn btn-sm flex-grow-1', book.completed ? 'btn-outline-success' : 'btn-success']"
            @click="$emit('toggle', book.id)"
          >{{ book.completed ? '✓ Прочитано' : 'Отметить' }}</button>

          <button class="btn btn-sm btn-outline-danger" @click="$emit('delete', book.id)">✕</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  transition: transform 0.2s, box-shadow 0.2s;
  border: none;
}
.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.15) !important;
}
.card-img-top {
  height: 180px;
  object-fit: cover;
}
.img-placeholder {
  height: 180px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 56px;
  background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
}
.badge-genre    { font-size: 11px; }
.card-title     { font-size: 15px; font-weight: 600; }
.completed-badge { font-size: 11px; }
.star-rating span {
  font-size: 18px;
  cursor: pointer;
  color: #ffc107;
}
.star-rating span:hover {
  transform: scale(1.2);
  display: inline-block;
}
</style>