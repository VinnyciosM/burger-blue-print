<script setup>
import { ref, watch, onMounted } from 'vue'
import IngredientSidebar from './components/IngredientSidebar.vue'
import BurgerBuilder from './components/BurgerBuilder.vue'
import ActionSidebar from './components/ActionSidebar.vue'

const activeIndex = ref(0)
const activeProjectId = ref(1) // Rastreia qual projeto está na tela

// 1. Carrega o histórico ou inicia com o primeiro projeto
const savedProjects = ref(JSON.parse(localStorage.getItem('drakon_history')) || [
  { 
    id: 1, 
    name: 'Lanche 1', 
    data: [{ name: '', layers: [] }, { name: '', layers: [] }, { name: '', layers: [] }] 
  }
])

// 2. Estado do editor sincronizado com o projeto ativo
const guides = ref(JSON.parse(localStorage.getItem('drakon_current_session')) || 
  JSON.parse(JSON.stringify(savedProjects.value[0].data))
)

// Sincroniza o editor de volta para a lista em tempo real
watch(guides, (newVal) => {
  localStorage.setItem('drakon_current_session', JSON.stringify(newVal))
  
  const index = savedProjects.value.findIndex(p => p.id === activeProjectId.value)
  if (index !== -1) {
    savedProjects.value[index].data = JSON.parse(JSON.stringify(newVal))
    savedProjects.value[index].name = newVal[0].name || newVal[1].name || newVal[2].name || `Lanche ${activeProjectId.value}`
  }
}, { deep: true })

watch(savedProjects, (newVal) => {
  localStorage.setItem('drakon_history', JSON.stringify(newVal))
}, { deep: true })

const handleNewProject = () => {
  const newId = savedProjects.value.length + 1
  const newProject = {
    id: newId,
    name: `Lanche ${newId}`,
    data: [{ name: '', layers: [] }, { name: '', layers: [] }, { name: '', layers: [] }]
  }
  savedProjects.value.push(newProject)
  activeProjectId.value = newId
  guides.value = JSON.parse(JSON.stringify(newProject.data))
  activeIndex.value = 0
}

const handleLoadProject = (project) => {
  activeProjectId.value = project.id
  guides.value = JSON.parse(JSON.stringify(project.data))
  activeIndex.value = 0
}

const handleSelectIngredient = (ingredient) => {
  guides.value[activeIndex.value].layers.unshift({
    ...ingredient,
    instanceId: Date.now() + Math.random()
  })
}

const handleDeleteProject = (projectId) => {
  // 1. Remove da lista de projetos salvos
  savedProjects.value = savedProjects.value.filter(p => p.id !== projectId)
  
  // 2. Se a lista ficar vazia, cria um projeto em branco por segurança
  if (savedProjects.value.length === 0) {
    const newId = 1
    const emptyData = [{ name: '', layers: [] }, { name: '', layers: [] }, { name: '', layers: [] }]
    savedProjects.value.push({ id: newId, name: 'Lanche 1', data: emptyData })
    activeProjectId.value = newId
    guides.value = JSON.parse(JSON.stringify(emptyData))
    activeIndex.value = 0
    return
  }

  // 3. Se apagou o projeto que estava aberto na tela, carrega o primeiro que sobrou
  if (activeProjectId.value === projectId) {
    handleLoadProject(savedProjects.value[0])
  }
}
</script>

<template>
  <div class="app-layout">
    <IngredientSidebar @select-ingredient="handleSelectIngredient" />
    
    <BurgerBuilder 
      :key="`builder-${activeIndex}`"
      v-model="guides[activeIndex].name" 
      v-model:layers="guides[activeIndex].layers" 
    />
    
    <ActionSidebar 
      :guides="guides" 
      :saved-projects="savedProjects"
      v-model:activeIndex="activeIndex" 
      @new-project="handleNewProject"
      @load-project="handleLoadProject"
      @delete-project="handleDeleteProject"
      :active-id="activeProjectId"
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