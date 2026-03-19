<script setup>
import { computed } from 'vue'

const props = defineProps(['filter', 'search', 'sortOrder', 'books'])
defineEmits(['update:filter', 'update:search', 'update:sortOrder'])

const filterOptions = [
  { value: 'all',      label: 'Все' },
  { value: 'unread',   label: 'Непрочитанные' },
  { value: 'read',     label: 'Прочитанные' },
  { value: 'favorite', label: '★ Избранные' },
]

const total     = computed(() => props.books.length)
const readCount = computed(() => props.books.filter(b => b.completed).length)
const leftCount = computed(() => total.value - readCount.value)
</script>

<template>
  <div class="sort-bar shadow-sm p-3 mb-4">
    <div class="row align-items-center g-2">

      <!-- Поиск -->
      <div class="col-md-4">
        <input
          :value="search"
          @input="$emit('update:search', $event.target.value)"
          type="text"
          class="form-control"
          placeholder="🔍 Поиск по названию или автору..."
        />
      </div>

      <!-- Фильтр -->
      <div class="col-md-4 d-flex gap-2 justify-content-center flex-wrap">
        <button
          v-for="opt in filterOptions" :key="opt.value"
          :class="['btn btn-sm', filter === opt.value ? 'btn-primary' : 'btn-outline-secondary']"
          @click="$emit('update:filter', opt.value)"
        >{{ opt.label }}</button>
      </div>

      <!-- Сортировка -->
      <div class="col-md-4">
        <select
          :value="sortOrder"
          @change="$emit('update:sortOrder', $event.target.value)"
          class="form-select form-select-sm"
        >
          <option value="newest">🕐 Сначала новые</option>
          <option value="oldest">🕐 Сначала старые</option>
          <option value="az">🔤 По названию А-Я</option>
          <option value="rating">⭐ По рейтингу</option>
        </select>
      </div>
    </div>

    <!-- Статистика -->
    <div class="mt-2 text-muted small text-center">
      Всего: <b>{{ total }}</b> |
      Прочитано: <b>{{ readCount }}</b> |
      Осталось: <b>{{ leftCount }}</b>
    </div>
  </div>
</template>

<style scoped>
.sort-bar {
  background: white;
  border-radius: 10px;
}
</style>