<script setup>
import { ref, watch, onMounted } from 'vue'
import IngredientSidebar from './components/IngredientSidebar.vue'
import BurgerBuilder from './components/BurgerBuilder.vue'
import ActionSidebar from './components/ActionSidebar.vue'

const activeIndex = ref(0)

// 1. Tenta carregar a sessão atual do LocalStorage; se não houver, inicia vazio
const guides = ref(JSON.parse(localStorage.getItem('drakon_current_session')) || [
  { name: '', layers: [] },
  { name: '', layers: [] },
  { name: '', layers: [] }
])

// 2. Lista de projetos arquivados no Gerenciador
const savedProjects = ref(JSON.parse(localStorage.getItem('drakon_history')) || [])

// --- LOGICA DE PERSISTÊNCIA ---

// Auto-save: Sempre que você mudar algo no visualizador, ele salva no navegador
watch(guides, (newVal) => {
  localStorage.setItem('drakon_current_session', JSON.stringify(newVal))
}, { deep: true })

// Salva o histórico sempre que um novo projeto for arquivado
watch(savedProjects, (newVal) => {
  localStorage.setItem('drakon_history', JSON.stringify(newVal))
}, { deep: true })

const activeProjectId = ref(1) // Começa no primeiro

const handleNewProject = () => {
  const newId = savedProjects.value.length + 1
  
  const newProject = {
    id: newId,
    name: `Lanche ${newId}`,
    data: [ { name: '', layers: [] }, { name: '', layers: [] }, { name: '', layers: [] } ]
  }

  // Usamos push para manter a ordem estável na lista
  savedProjects.value.push(newProject)
  
  // Muda o foco para a nova guia criada
  activeProjectId.value = newId
  guides.value = newProject.data
  activeIndex.value = 0
}

const handleLoadProject = (project) => {
  activeProjectId.value = project.id // Atualiza qual ID está sendo editado
  guides.value = JSON.parse(JSON.stringify(project.data))
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