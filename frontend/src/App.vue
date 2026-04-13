<script setup>
import { ref } from 'vue'
import IngredientSidebar from './components/IngredientSidebar.vue'
import BurgerBuilder from './components/BurgerBuilder.vue'
import ActionSidebar from './components/ActionSidebar.vue' // O novo componente

const burgerLayers = ref([])
const burgerName = ref('NOVO HAMBÚRGUER')

const handleAddIngredient = (ingredient) => {
  burgerLayers.value.push({
    ...ingredient,
    instanceId: Date.now() + Math.random().toString(36).substring(2, 9)
  })
}

const handleRemoveLayer = (index) => {
  burgerLayers.value.splice(index, 1)
}

const saveBurgerData = (layers) => {
  console.log('Salvando dados no banco...', layers)
  // Aqui você faria o POST para sua API (ex: Cloudflare D1 / Hono)
}
</script>

<template>
  <div class="app-layout">
    <IngredientSidebar 
      class="hide-on-print"
      @select-ingredient="handleAddIngredient" 
    />

    <BurgerBuilder 
      class="hide-on-print"
      :layers="burgerLayers"
      @update:layers="burgerLayers = $event"
      @remove-layer="handleRemoveLayer"
    />

    <ActionSidebar 
      :layers="burgerLayers"
      :burgerName="burgerName"
      @save="saveBurgerData"
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