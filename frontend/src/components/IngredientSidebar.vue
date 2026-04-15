<script setup>
import { ref, computed } from 'vue'
import ingredientsData from '../ingredients.json'
import NewIngredientCard from './NewIngredientCard.vue'
import IngredientCatalog from './IngredientCatalog.vue'

const emit = defineEmits(['select-ingredient'])

const searchQuery = ref('')
const activeCategory = ref('TODOS')
const isNewModalOpen = ref(false)
const isCatalogModalOpen = ref(false)

// Lista de categorias únicas (sem o TODOS aqui, pois ele é um estado global)
const pureCategories = computed(() => {
  const all = ingredientsData.map(item => item.category)
  return [...new Set(all)]
})

// Abas superiores incluindo o botão TODOS
const tabCategories = computed(() => ['TODOS', ...pureCategories.value])

// LÓGICA DE AGRUPAMENTO: Organiza os ingredientes por seções/divisórias
const groupedIngredients = computed(() => {
  // Filtro de busca inicial
  const filtered = ingredientsData.filter(item => 
    item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )

  // Criamos o mapa de grupos
  const groups = pureCategories.value.map(cat => ({
    name: cat,
    items: filtered.filter(item => item.category === cat)
  })).filter(g => g.items.length > 0) // Remove grupos vazios na busca

  // Se estiver em TODOS, retorna a ordem padrão
  if (activeCategory.value === 'TODOS') return groups

  // Se uma categoria estiver ativa, movemos o grupo dela para o primeiro lugar
  const priorityGroup = groups.filter(g => g.name === activeCategory.value)
  const otherGroups = groups.filter(g => g.name !== activeCategory.value)

  return [...priorityGroup, ...otherGroups]
})

const selectIngredient = (ingredient) => {
  emit('select-ingredient', ingredient)
}
</script>

<template>
  <aside class="sidebar">
    <div class="search-row">
        <input 
          type="text" 
          v-model="searchQuery" 
          placeholder="Buscar ingrediente..." 
          class="search-input"
        />
        <button class="btn-add-ingredient" @click="isNewModalOpen = true" title="Novo Ingrediente">
          +
        </button>
      </div>

    <nav class="category-tabs">
      <button 
        v-for="cat in tabCategories" 
        :key="cat"
        class="tab-btn"
        :class="{ active: activeCategory === cat }"
        @click="activeCategory = cat"
      >
        {{ cat }}
      </button>
    </nav>

    <div class="scroll-container">
      <section 
        v-for="group in groupedIngredients" 
        :key="group.name" 
        class="category-section"
      >
        <div class="category-divider">
          <span class="divider-text">{{ group.name }}</span>
          <div class="divider-line"></div>
        </div>

        <div class="ingredients-flex">
          <div 
            v-for="item in group.items" 
            :key="item.id" 
            class="ingredient-card"
            @click="selectIngredient(item)"
          >
            <div class="card-image-wrapper">
              <img :src="item.image" :alt="item.name" class="card-image" />
            </div>
            <p class="card-name">{{ item.name }}</p>
          </div>
        </div>
      </section>
    </div>
    <div class="sidebar-footer">
      <button class="btn-catalog" @click="isCatalogModalOpen = true">
        <span class="icon">📋</span>
        VER BIBLIOTECA COMPLETA
      </button>
    </div>
  </aside>
  <NewIngredientCard 
    v-if="isNewModalOpen" 
    @close="isNewModalOpen = false"
    @save="isNewModalOpen = false"
  />
  <IngredientCatalog
    v-if="isCatalogModalOpen"
    :ingredients="ingredientsData"
    @close="isCatalogModalOpen = false"
  />
</template>

<style scoped>
.sidebar {
  width: 30%;
  height: 100vh;
  background-color: #f9fafb;
  border-right: 1px solid #e5e7eb;
  display: flex;
  flex-direction: column;
}

.sidebar-header { padding: 15px; background: white; }
.title { font-size: 11px; font-weight: 900; color: #1f2937; margin-bottom: 8px; }

.search-input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #e5e7eb;
  border-radius: 6px;
  font-size: 13px;
  outline: none;
}

.category-tabs {
  display: flex;
  gap: 5px;
  padding: 10px;
  overflow-x: auto;
  background: white;
  border-bottom: 1px solid #eee;
}

.tab-btn {
  padding: 6px 12px;
  font-size: 9px;
  font-weight: 800;
  white-space: nowrap;
  border: none;
  background: #f3f4f6;
  color: #6b7280;
  cursor: pointer;
  border-radius: 4px;
  text-transform: uppercase;
}
.tab-btn.active { background: #fb923c; color: white; }

.scroll-container {
  flex: 1;
  padding: 10px 15px;
  overflow-y: auto;
}

/* --- ESTILO DAS DIVISÓRIAS --- */
.category-section { margin-bottom: 25px; }

.category-divider {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
}

.divider-text {
  font-size: 10px;
  font-weight: 900;
  color: #9ca3af;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.divider-line {
  flex: 1;
  height: 1px;
  background-color: #e5e7eb;
}

/* --- GRID DE ITENS --- */
.ingredients-flex {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.ingredient-card {
  background: white;
  border: 1px solid #f1f1f1;
  border-radius: 8px;
  padding: 10px;
  cursor: pointer;
  flex: 0 0 calc(50% - 5px); /* Ajustado para 2 por linha com gap */
  max-width: 140px;
  aspect-ratio: 1 / 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: all 0.2s;
}

.ingredient-card:hover { border-color: #fb923c; }

.card-image-wrapper {
  width: 100%;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.card-image {
  max-width: 85%;
  max-height: 85%;
  object-fit: contain;
  mix-blend-mode: multiply;
}

.card-name {
  font-size: 9px;
  font-weight: 800;
  color: #374151;
  text-align: center;
  text-transform: uppercase;
  margin-top: 5px;
}

/* Scrollbar fina */
.scroll-container::-webkit-scrollbar { width: 4px; }
.scroll-container::-webkit-scrollbar-thumb { background: #e5e7eb; border-radius: 10px; }

/* Container pai (a Sidebar) */
.sidebar {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

/* Linha da Busca */
.search-row {
  display: flex;
  gap: 8px;
  padding: 10px 0;
}

.search-input {
  flex: 1;
  padding: 10px;
  border: 1px solid #d1d5db;
  border-radius: 8px;
}

.btn-add-ingredient{
  width: 42px;
  background: #fb923c; /* Laranja Drakon */
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: bold;
  font-size: 20px;
  cursor: pointer;
}

/* Rodapé Fixo */
.sidebar-footer {
  padding: 15px 20px;
  background: #fff;
  border-top: 1px solid #f3f4f6;
  margin-top: auto; /* Joga o rodapé para o final do flex */
}

.btn-catalog {
  width: 100%;
  padding: 12px;
  background: #111827; /* Dark Drakon */
  color: #fff;
  border: none;
  border-radius: 8px;
  font-size: 11px;
  font-weight: 900;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}
</style>