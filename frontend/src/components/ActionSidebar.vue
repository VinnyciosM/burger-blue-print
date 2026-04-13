<script setup>
import { ref } from 'vue'

const props = defineProps({
  layers: {
    type: Array,
    required: true
  },
  burgerName: {
    type: String,
    default: 'NOVO HAMBÚRGUER'
  }
})

const emit = defineEmits(['save', 'load-burger'])

// Estado para controlar o Modal do Visualizador
const isViewerOpen = ref(false)

// Mock de hambúrgueres já criados (Depois virá do seu banco D1)
const savedBurgers = ref([
  { id: 1, name: 'Cheddar Melt Supremo', date: '12/04/2026', items: 6 },
  { id: 2, name: 'Drakon Classic', date: '10/04/2026', items: 5 },
  { id: 3, name: 'Smash Bacon Kids', date: '05/04/2026', items: 4 }
])

const handleExportPDF = () => {
  window.print()
}

const handleSave = () => {
  emit('save', props.layers)
}

const openViewer = () => {
  isViewerOpen.value = true
}

const closeViewer = () => {
  isViewerOpen.value = false
}

const selectSavedBurger = (burger) => {
  emit('load-burger', burger)
  closeViewer()
}
</script>

<template>
  <aside class="right-column print-area">
    <h2 class="section-title hide-on-print">AÇÕES & PRÉ-VISUALIZAÇÃO</h2>

    <div class="actions-grid hide-on-print">
      <button class="btn btn-dark" @click="handleSave">SALVAR</button>
      <button class="btn btn-outline" @click="handleExportPDF">EXPORTAR (PDF)</button>
    </div>
    
    <button class="btn btn-viewer full-width hide-on-print" @click="openViewer">
      <svg class="icon-inline" viewBox="0 0 24 24"><path fill="currentColor" d="M9 3V18H12V3H9M12 21A3 3 0 0 1 9 24H6A3 3 0 0 1 3 21V6A3 3 0 0 1 6 3H9V6H6V21H9V21A3 3 0 0 1 12 21M15 3H18A3 3 0 0 1 21 6V21A3 3 0 0 1 18 24H15A3 3 0 0 1 12 21V18H15V21H18V6H15V3Z"/></svg>
      MEUS GUIAS CRIADOS
    </button>

    <div class="preview-container">
      <div class="preview-card">
        <h3 class="preview-title">{{ burgerName }}</h3>
        
        <div class="preview-stack">
          <div v-if="layers.length === 0" class="empty-preview">Nenhum ingrediente.</div>
          <div v-for="(layer, index) in layers" :key="layer.instanceId" class="preview-item">
            <img :src="layer.image" :alt="layer.name" class="preview-image" />
            <div class="preview-label">
              <span class="label-line"></span>
              <span class="label-text">{{ layer.name }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <transition name="fade">
      <div v-if="isViewerOpen" class="viewer-overlay hide-on-print" @click.self="closeViewer">
        <div class="viewer-modal">
          <div class="viewer-header">
            <h3>Biblioteca de Guias</h3>
            <button class="close-btn" @click="closeViewer">×</button>
          </div>
          
          <div class="viewer-list">
            <div 
              v-for="burger in savedBurgers" 
              :key="burger.id" 
              class="burger-card-item"
              @click="selectSavedBurger(burger)"
            >
              <div class="burger-info">
                <strong>{{ burger.name }}</strong>
                <span>Criado em: {{ burger.date }} • {{ burger.items }} itens</span>
              </div>
              <div class="burger-action-icon">→</div>
            </div>
          </div>

          <div v-if="savedBurgers.length === 0" class="viewer-empty">
            Você ainda não salvou nenhum guia.
          </div>
        </div>
      </div>
    </transition>
  </aside>
</template>

<style scoped>
/* (Mantendo o estilo anterior e adicionando o novo) */
.right-column {
  width: 33.33vw;
  height: 100vh;
  background-color: #ffffff;
  border-left: 1px solid #e5e7eb;
  padding: 20px;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
}

.section-title { font-size: 14px; font-weight: 800; color: #374151; margin-bottom: 20px; text-align: center; }

.actions-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 10px; }

.btn { padding: 10px; font-size: 12px; font-weight: 700; border-radius: 6px; cursor: pointer; transition: all 0.2s; text-align: center; }
.btn-dark { background-color: #374151; color: white; border: 1px solid #374151; }
.btn-outline { background-color: white; color: #374151; border: 1px solid #d1d5db; }
.btn-viewer { background-color: #f3f4f6; color: #1f2937; border: 2px solid #e5e7eb; display: flex; align-items: center; justify-content: center; gap: 8px; margin-bottom: 20px; }
.btn-viewer:hover { background-color: #e5e7eb; }

.icon-inline { width: 16px; height: 16px; }
.full-width { width: 100%; }

/* Preview Card */
.preview-container { flex: 1; display: flex; justify-content: center; overflow-y: auto; }
.preview-card { width: 100%; max-width: 260px; padding: 20px; border-radius: 12px; border: 1px solid #eee; display: flex; flex-direction: column; align-items: center; }
.preview-title { font-size: 14px; font-weight: 900; margin-bottom: 25px; text-transform: uppercase; }
.preview-stack { display: flex; flex-direction: column; align-items: center; }
.preview-item { position: relative; display: flex; align-items: center; margin-bottom: -25px; }
.preview-image { width: 100px; filter: drop-shadow(0 4px 4px rgba(0,0,0,0.1)); }
.preview-label { position: absolute; left: 105%; display: flex; align-items: center; width: max-content; }
.label-line { width: 15px; height: 1px; background: #ccc; margin-right: 5px; }
.label-text { font-size: 8px; font-weight: 800; color: #666; text-transform: uppercase; }

/* MODAL DO VISUALIZADOR */
.viewer-overlay {
  position: fixed;
  top: 0; left: 0;
  width: 100vw; height: 100vh;
  background: rgba(0,0,0,0.5);
  display: flex; justify-content: center; align-items: center;
  z-index: 9999;
}

.viewer-modal {
  background: white;
  width: 90%; max-width: 500px;
  max-height: 80vh;
  border-radius: 16px;
  display: flex; flex-direction: column;
  box-shadow: 0 20px 25px -5px rgba(0,0,0,0.2);
  overflow: hidden;
}

.viewer-header {
  padding: 20px;
  border-bottom: 1px solid #eee;
  display: flex; justify-content: space-between; align-items: center;
}

.viewer-header h3 { font-size: 16px; font-weight: 800; margin: 0; }
.close-btn { background: none; border: none; font-size: 24px; cursor: pointer; color: #999; }

.viewer-list { flex: 1; overflow-y: auto; padding: 15px; }

.burger-card-item {
  padding: 15px;
  border: 1px solid #eee;
  border-radius: 10px;
  margin-bottom: 10px;
  display: flex; justify-content: space-between; align-items: center;
  cursor: pointer;
  transition: background 0.2s;
}

.burger-card-item:hover { background: #f9fafb; border-color: #fb923c; }

.burger-info strong { display: block; font-size: 14px; color: #1f2937; }
.burger-info span { font-size: 11px; color: #6b7280; }
.burger-action-icon { font-weight: bold; color: #fb923c; }

/* Animação do Modal */
.fade-enter-active, .fade-leave-active { transition: opacity 0.3s; }
.fade-enter-from, .fade-leave-to { opacity: 0; }

@media print {
  .hide-on-print { display: none !important; }
}
</style>