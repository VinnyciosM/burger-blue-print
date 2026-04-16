<script setup>
import { ref, watch, onMounted } from 'vue'
import IngredientSidebar from './components/IngredientSidebar.vue'
import BurgerBuilder from './components/BurgerBuilder.vue'
import ActionSidebar from './components/ActionSidebar.vue'

// Índice da coluna (Guia) que está sendo editada no momento (0, 1 ou 2)
const activeIndex = ref(0)

// 1. Estado das 3 colunas atuais (Sessão Ativa)
// Tenta carregar do LocalStorage para não perder dados ao atualizar a página
const guides = ref(JSON.parse(localStorage.getItem('drakon_current_session')) || [
  { name: '', layers: [] },
  { name: '', layers: [] },
  { name: '', layers: [] }
])

// 2. Histórico de projetos arquivados no Gerenciador
const savedProjects = ref(JSON.parse(localStorage.getItem('drakon_history')) || [])

// --- LÓGICA DE PERSISTÊNCIA (LocalStorage) ---

// Auto-save: Salva as 3 colunas atuais sempre que houver qualquer mudança
watch(guides, (newVal) => {
  localStorage.setItem('drakon_current_session', JSON.stringify(newVal))
}, { deep: true })

// Salva o histórico de projetos arquivados
watch(savedProjects, (newVal) => {
  localStorage.setItem('drakon_history', JSON.stringify(newVal))
}, { deep: true })

// --- AÇÕES DO SISTEMA ---

/**
 * Arquiva o lanche atual no gerenciador e limpa o visualizador para um novo início
 */
const handleNewProject = () => {
  // Cria uma cópia (snapshot) dos dados atuais
  const projectToArchive = {
    id: savedProjects.value.length + 1, // ID Numérico sequencial
    // Usa o nome de qualquer uma das guias como título do projeto
    name: guides.value[0].name || guides.value[1].name || guides.value[2].name || `Lanche ${savedProjects.value.length + 1}`,
    data: JSON.parse(JSON.stringify(guides.value)), // Cópia profunda para evitar vínculos
    timestamp: new Date().toLocaleString()
  }

  // Adiciona ao final da lista para manter a ordem estável no gerenciador
  savedProjects.value.push(projectToArchive)

  // Reseta as 3 colunas para o estado vazio
  guides.value = [
    { name: '', layers: [] },
    { name: '', layers: [] },
    { name: '', layers: [] }
  ]
  
  activeIndex.value = 0
  alert("Projeto arquivado e visualizador limpo!")
}

/**
 * Carrega um projeto salvo anteriormente de volta para as 3 colunas de edição
 */
const handleLoadProject = (project) => {
  // Substitui os guias atuais pelos dados do projeto selecionado
  guides.value = JSON.parse(JSON.stringify(project.data))
  activeIndex.value = 0 // Foca na primeira coluna por padrão
}

/**
 * Adiciona um ingrediente selecionado na coluna (guia) ativa
 */
const handleSelectIngredient = (ingredient) => {
  guides.value[activeIndex.value].layers.unshift({
    ...ingredient,
    instanceId: Date.now() + Math.random() // Gera ID único para cada camada
  })
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