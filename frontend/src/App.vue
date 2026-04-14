<script setup>
import { ref } from 'vue'
import IngredientSidebar from './components/IngredientSidebar.vue'
import BurgerBuilder from './components/BurgerBuilder.vue'
import ActionSidebar from './components/ActionSidebar.vue'

// 1. Estado centralizado do nome e das camadas
const burgerName = ref('')
const burgerLayers = ref([])

const handleSelectIngredient = (ingredient) => {
  burgerLayers.value.unshift({
    ...ingredient,
    instanceId: Date.now() + Math.random()
  })
}
</script>

<template>
  <div class="app-layout">
    <IngredientSidebar @select-ingredient="handleSelectIngredient" />
    
    <BurgerBuilder 
      v-model="burgerName" 
      v-model:layers="burgerLayers" 
    />
    
    <ActionSidebar 
      :layers="burgerLayers" 
      :name="burgerName" 
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