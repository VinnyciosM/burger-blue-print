<script setup>
import { ref, computed } from 'vue'

const emit = defineEmits(['select-ingredient'])

const searchQuery = ref('')
const activeCategory = ref('PÃES')

const ingredients = ref([
  { id: 1, name: 'Pão Brioche', category: 'PÃES', image: 'https://placehold.co/100x100?text=Pao' },
  { id: 2, name: 'Pão Australiano', category: 'PÃES', image: 'https://placehold.co/100x100?text=Pao+Aus' },
  { id: 3, name: 'Burger Bovino', category: 'PROTEÍNAS', image: 'https://placehold.co/100x100?text=Carne' },
  { id: 4, name: 'Bacon Crispy', category: 'PROTEÍNAS', image: 'https://placehold.co/100x100?text=Bacon' },
  { id: 5, name: 'Cheddar', category: 'QUEIJOS', image: 'https://placehold.co/100x100?text=Queijo' },
  { id: 6, name: 'Alface Americana', category: 'OUTROS', image: 'https://placehold.co/100x100?text=Alface' },
])

const categories = ['PÃES', 'PROTEÍNAS', 'QUEIJOS', 'OUTROS']

const filteredIngredients = computed(() => {
  return ingredients.value.filter(item => {
    const matchesCategory = item.category === activeCategory.value
    const matchesSearch = item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
    return matchesCategory && matchesSearch
  })
})

const handleSelectItem = (item) => {
  emit('select-ingredient', item)
}
</script>

<template>
  <aside class="sidebar-container">
    <h2 class="sidebar-title">DESPENSA (ATIVOS)</h2>

    <div class="search-box">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Busca..."
        class="search-input"
      />
    </div>

    <div class="categories-nav">
      <button
        v-for="cat in categories"
        :key="cat"
        @click="activeCategory = cat"
        :class="{ 'active': activeCategory === cat }"
        class="category-btn"
      >
        {{ cat }}
      </button>
    </div>

    <div class="ingredients-grid-container">
      <div class="ingredients-grid">
        <div
          v-for="item in filteredIngredients"
          :key="item.id"
          class="ingredient-card"
          @click="handleSelectItem(item)"
        >
          <div class="image-wrapper">
             <svg class="drag-icon" viewBox="0 0 24 24"><path fill="currentColor" d="M7 19V17H9V19H7M11 19V17H13V19H11M15 19V17H17V19H15M7 15V13H9V15H7M11 15V13H13V15H11M15 15V13H17V15H15M7 11V9H9V11H7M11 11V9H13V11H11M15 11V9H17V11H15M7 7V5H9V7H7M11 7V5H13V7H11M15 7V5H17V7H15Z" /></svg>
             <img :src="item.image" :alt="item.name" />
          </div>
          <span class="ingredient-name">{{ item.name }}</span>
        </div>
      </div>
    </div>
  </aside>
</template>

<style scoped>
.sidebar-container {
  /* Ajustes de dimensões solicitados */
  width: 33.33vw; 
  height: 100vh;
  
  background-color: #ffffff;
  border-right: 1px solid #e5e7eb;
  padding: 20px;
  display: flex;
  flex-direction: column;
  box-sizing: border-box; /* Garante que o padding não aumente a largura */
}

.sidebar-title {
  font-size: 14px;
  font-weight: 800;
  color: #374151;
  margin-bottom: 18px;
  letter-spacing: 0.05em;
}

.search-input {
  width: 100%;
  padding: 10px 14px;
  background-color: #f9fafb;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  margin-bottom: 20px;
  font-size: 14px;
  box-sizing: border-box;
}

.categories-nav {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 24px;
}

.category-btn {
  padding: 6px 12px;
  font-size: 11px;
  font-weight: 700;
  background: white;
  border: 1px solid #e5e7eb;
  border-radius: 4px;
  cursor: pointer;
  color: #6b7280;
  transition: all 0.2s;
}

.category-btn.active {
  background-color: #1f2937;
  color: white;
  border-color: #1f2937;
}

.ingredients-grid-container {
  flex: 1;
  overflow-y: auto;
  padding-right: 4px;
}

.ingredients-grid {
  display: grid;
  /* Aumentei um pouco o gap já que a coluna agora é maior */
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
}

.ingredient-card {
  cursor: pointer;
  text-align: center;
}

.image-wrapper {
  position: relative;
  aspect-ratio: 1/1;
  border: 2px solid #f3f4f6;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 10px;
  transition: all 0.2s;
  background-color: #fff;
}

.ingredient-card:hover .image-wrapper {
  border-color: #fb923c;
  transform: translateY(-2px);
  box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
}

.image-wrapper img {
  max-width: 90%;
  max-height: 90%;
  object-fit: contain;
}

.drag-icon {
  position: absolute;
  top: 6px;
  left: 6px;
  width: 14px;
  height: 14px;
  color: #d1d5db;
  opacity: 0;
}

.ingredient-card:hover .drag-icon {
  opacity: 1;
}

.ingredient-name {
  display: block;
  font-size: 11px;
  margin-top: 6px;
  color: #4b5563;
  font-weight: 500;
}

/* Scrollbar fina para não poluir o visual largo */
.ingredients-grid-container::-webkit-scrollbar {
  width: 5px;
}
.ingredients-grid-container::-webkit-scrollbar-thumb {
  background: #e5e7eb;
  border-radius: 10px;
}
</style>