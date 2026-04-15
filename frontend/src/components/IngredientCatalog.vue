<script setup>
import { ref, computed } from 'vue'
import DeleteConfirmModal from './DeleteConfirmModal.vue'
import NewIngredientCard from './NewIngredientCard.vue' //

const props = defineProps({
    ingredients: { type: Array, required: true }
})

const emit = defineEmits(['close', 'delete-ingredient', 'add-ingredient']) //

const searchQuery = ref('')
const activeCategory = ref('TODOS')
const itemToDelete = ref(null)
const isNewModalOpen = ref(false) // Controla a abertura do NewIngredientCard

// 1. Extrai categorias únicas para as abas
const categories = computed(() => {
    const cats = props.ingredients.map(i => i.category)
    return ['TODOS', ...new Set(cats)]
})

// 2. Filtra os ingredientes com base na busca e na aba selecionada
const filteredIngredients = computed(() => {
    const query = searchQuery.value.toLowerCase()
    return props.ingredients.filter(item => {
        const matchesSearch = item.name.toLowerCase().includes(query)
        const matchesTab = activeCategory.value === 'TODOS' || item.category === activeCategory.value
        return matchesSearch && matchesTab
    })
})

// 3. Agrupa os itens filtrados por categoria para criar as divisórias
const groupedIngredients = computed(() => {
    const groups = {}
    filteredIngredients.value.forEach(item => {
        if (!groups[item.category]) groups[item.category] = []
        groups[item.category].push(item)
    })
    return groups
})

const confirmDelete = () => {
    if (itemToDelete.value) {
        emit('delete-ingredient', itemToDelete.value.id)
        itemToDelete.value = null
    }
}

// Repassa o salvamento do novo ingrediente para o componente pai
const handleSaveNew = (newIng) => {
    emit('add-ingredient', newIng)
    isNewModalOpen.value = false
}
</script>

<template>
    <div class="overlay" @click.self="$emit('close')">
        <div class="catalog-card">

            <div class="card-header">
                <div class="header-main">
                    <h2 class="title">GESTÃO DE BIBLIOTECA</h2>
                    <button class="btn-close" @click="$emit('close')">&times;</button>
                </div>

                <div class="search-container">
                    <div class="search-row">
                        <input type="text" v-model="searchQuery" placeholder="Buscar na biblioteca..."
                            class="search-input" />
                        <button class="btn-add-catalog" @click="isNewModalOpen = true"
                            title="Adicionar Novo Ingrediente">
                            +
                        </button>
                    </div>
                </div>

                <nav class="category-tabs">
                    <button v-for="cat in categories" :key="cat" class="tab-btn"
                        :class="{ active: activeCategory === cat }" @click="activeCategory = cat">
                        {{ cat }}
                    </button>
                </nav>
            </div>

            <div class="catalog-content">
                <div v-if="filteredIngredients.length === 0" class="empty-results">
                    Nenhum ingrediente encontrado.
                </div>

                <section v-for="(items, catName) in groupedIngredients" :key="catName" class="category-section">
                    <div class="category-divider">
                        <span class="divider-text">{{ catName }}</span>
                        <div class="divider-line"></div>
                    </div>

                    <div class="catalog-grid">
                        <div v-for="item in items" :key="item.id" class="ingredient-item">
                            <button class="btn-delete-trigger" @click.stop="itemToDelete = item">&times;</button>
                            <div class="image-box">
                                <img :src="item.image" :alt="item.name" class="item-image" />
                            </div>
                            <p class="item-name">{{ item.name }}</p>
                        </div>
                    </div>
                </section>
            </div>
        </div>
    </div>

    <DeleteConfirmModal v-if="itemToDelete" :item-name="itemToDelete.name" @confirm="confirmDelete"
        @cancel="itemToDelete = null" />

    <NewIngredientCard v-if="isNewModalOpen" @close="isNewModalOpen = false" @save="handleSaveNew" />
</template>

<style scoped>
.overlay {
    position: fixed;
    inset: 0;
    background: rgba(17, 24, 39, 0.95);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 10000;
    padding: 20px;
}

.catalog-card {
    background: #fff;
    width: 100%;
    max-width: 900px;
    height: 90vh;
    border-radius: 16px;
    display: flex;
    flex-direction: column;
    box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
    overflow: hidden;
}

.card-header {
    padding: 30px 30px 10px 30px;
    display: flex;
    flex-direction: column;
    gap: 20px;
    background: #fff;
    z-index: 10;
}

.header-main {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.title {
    font-size: 14px;
    font-weight: 900;
    letter-spacing: 1px;
    color: #111827;
    margin: 0;
}

.btn-close {
    background: none;
    border: none;
    font-size: 28px;
    color: #9ca3af;
    cursor: pointer;
}

/* Busca e Botão Novo */
.search-row {
    display: flex;
    gap: 10px;
}

.search-input {
    flex: 1;
    padding: 12px 16px;
    border: 1px solid #e5e7eb;
    border-radius: 8px;
    font-size: 14px;
    outline: none;
}

.search-input:focus {
    border-color: #fb923c;
}

.btn-add-catalog {
    width: 48px;
    background: #fb923c;
    /* Laranja Drakon */
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 24px;
    font-weight: bold;
    cursor: pointer;
    transition: background 0.2s;
}

.btn-add-catalog:hover {
    background: #ea580c;
}

.category-tabs {
    display: flex;
    gap: 8px;
    overflow-x: auto;
    padding-bottom: 10px;
}

.tab-btn {
    padding: 8px 16px;
    font-size: 10px;
    font-weight: 800;
    border: none;
    background: #f3f4f6;
    color: #6b7280;
    cursor: pointer;
    border-radius: 6px;
    text-transform: uppercase;
    white-space: nowrap;
}

.tab-btn.active {
    background: #fb923c;
    color: white;
}

/* Conteúdo e Grade */
.catalog-content {
    flex: 1;
    overflow-y: auto;
    padding: 10px 30px 30px 30px;
}

.category-section {
    margin-bottom: 30px;
}

.category-divider {
    display: flex;
    align-items: center;
    gap: 15px;
    margin-bottom: 15px;
}

.divider-text {
    font-size: 11px;
    font-weight: 900;
    color: #9ca3af;
    text-transform: uppercase;
}

.divider-line {
    flex: 1;
    height: 1px;
    background: #f3f4f6;
}

.catalog-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    gap: 15px;
}

.ingredient-item {
    position: relative;
    background: #f9fafb;
    border: 1px solid #e5e7eb;
    border-radius: 12px;
    padding: 15px;
    display: flex;
    flex-direction: column;
    align-items: center;
    transition: all 0.2s;
}

.ingredient-item:hover {
    border-color: #fb923c;
    background: #fff;
}

.btn-delete-trigger {
    position: absolute;
    top: 8px;
    right: 8px;
    width: 22px;
    height: 22px;
    background: #ef4444;
    color: white;
    border: none;
    border-radius: 50%;
    font-size: 14px;
    font-weight: bold;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0;
    transition: opacity 0.2s;
}

.ingredient-item:hover .btn-delete-trigger {
    opacity: 1;
}

.image-box {
    width: 80px;
    height: 80px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 8px;
}

.item-image {
    max-width: 100%;
    max-height: 100%;
    object-fit: contain;
    mix-blend-mode: multiply;
}

.item-name {
    font-size: 10px;
    font-weight: 800;
    color: #1f2937;
    text-align: center;
    text-transform: uppercase;
    margin: 0;
}

.empty-results {
    text-align: center;
    padding: 40px;
    color: #9ca3af;
    font-weight: 700;
}

.catalog-content::-webkit-scrollbar {
    width: 6px;
}

.catalog-content::-webkit-scrollbar-thumb {
    background: #e5e7eb;
    border-radius: 10px;
}
</style>