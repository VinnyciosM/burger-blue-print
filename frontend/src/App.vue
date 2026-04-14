<script setup>
import { ref } from 'vue'
import IngredientSidebar from './components/IngredientSidebar.vue'
import BurgerBuilder from './components/BurgerBuilder.vue'
import ActionSidebar from './components/ActionSidebar.vue'

// --- STATE MANAGEMENT ---

// Index of the guide currently being edited (0, 1, or 2)
const activeIndex = ref(0)

// Array containing the data for all 3 guides on the A4 sheet
const guides = ref([
  { name: '', layers: [] }, // Guide 1
  { name: '', layers: [] }, // Guide 2
  { name: '', layers: [] }  // Guide 3
])

// --- METHODS ---

/**
 * Adds a new ingredient to the top of the currently active guide
 * @param {Object} ingredient - The ingredient object from JSON
 */
const handleSelectIngredient = (ingredient) => {
  // We use unshift to add to the top (visual stack order)
  guides.value[activeIndex.value].layers.unshift({
    ...ingredient,
    instanceId: Date.now() + Math.random() // Unique ID for Vue's v-for and Drag & Drop
  })
}
</script>

<template>
  <div class="app-layout">
    <IngredientSidebar 
      @select-ingredient="handleSelectIngredient" 
    />
    
    <BurgerBuilder 
      :key="`builder-${activeIndex}`"
      v-model="guides[activeIndex].name" 
      v-model:layers="guides[activeIndex].layers" 
    />
    
    <ActionSidebar 
      :guides="guides" 
      v-model:activeIndex="activeIndex" 
    />
  </div>
</template>

<style scoped>
.app-layout {
  display: flex;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background-color: #f3f4f6;
}

:global(body) {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: system-ui, -apple-system, sans-serif;
}

/* Esconde as colunas 1 e 2 na hora de gerar o PDF */
@media print {
  .hide-on-print {
    display: none !important;
  }
}
</style>