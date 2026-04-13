<script setup>
import { ref } from 'vue'

// Propriedade que recebe a lista de ingredientes montados do componente pai
const props = defineProps({
  layers: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['update:layers', 'remove-layer'])

// Estado para controlar qual item está sendo arrastado
const draggedIndex = ref(null)

// Inicia o arrasto
const onDragStart = (index, event) => {
  draggedIndex.value = index
  // Efeito visual nativo do cursor
  event.dataTransfer.effectAllowed = 'move'
  // Deixa o item ligeiramente transparente enquanto arrasta (opcional)
  setTimeout(() => {
    event.target.classList.add('dragging')
  }, 0)
}

// Finaliza o visual de arrasto
const onDragEnd = (event) => {
  event.target.classList.remove('dragging')
  draggedIndex.value = null
}

// Solta o item na nova posição
const onDrop = (dropIndex) => {
  if (draggedIndex.value === null || draggedIndex.value === dropIndex) return

  // Cria uma cópia do array para não mutar a prop diretamente (boa prática Vue)
  const newLayers = [...props.layers]
  
  // Remove o item da posição original
  const [movedItem] = newLayers.splice(draggedIndex.value, 1)
  
  // Insere o item na nova posição
  newLayers.splice(dropIndex, 0, movedItem)

  // Emite a nova lista atualizada para o componente pai
  emit('update:layers', newLayers)
}

const handleRemove = (index) => {
  emit('remove-layer', index)
}
</script>

<template>
  <main class="middle-column">
    <div class="header-section">
      <h2 class="section-title">MONTAGEM (EM CONSTRUÇÃO)</h2>
      <div class="burger-name-input">NOVO HAMBÚRGUER (EM CONSTRUÇÃO)</div>
    </div>

    <div class="burger-stack-area">
      <div class="center-guide-line"></div>

      <div class="layers-container">
        <div v-if="layers.length === 0" class="empty-state">
          <p>Clique nos itens da despensa para começar a montar.</p>
        </div>

        <transition-group name="list" tag="div" class="stack-list">
          <div
            v-for="(layer, index) in layers"
            :key="layer.instanceId" 
            class="layer-item"
            draggable="true"
            @dragstart="onDragStart(index, $event)"
            @dragend="onDragEnd"
            @dragover.prevent
            @dragenter.prevent
            @drop="onDrop(index)"
          >
            <div class="drag-handle">
              <svg viewBox="0 0 24 24" class="icon"><path fill="currentColor" d="M12 5.83L15.17 9l1.41-1.41L12 3 7.41 7.59 8.83 9 12 5.83zm0 12.34L8.83 15l-1.41 1.41L12 21l4.59-4.59L15.17 15 12 18.17z"/></svg>
            </div>

            <div class="layer-content">
              <span class="layer-text">{{ layer.name }}</span>
              <span class="layer-category">{{ layer.category }}</span>
            </div>

            <button class="remove-btn" @click="handleRemove(index)" title="Remover item">
              <svg viewBox="0 0 24 24" class="icon icon-trash"><path fill="currentColor" d="M19 4h-3.5l-1-1h-5l-1 1H5v2h14M6 19a2 2 0 0 0 2 2h8a2 2 0 0 0 2-2V7H6v12Z"/></svg>
            </button>
          </div>
        </transition-group>
      </div>
    </div>
  </main>
</template>

<style scoped>
.middle-column {
  flex: 1; /* Ocupa o espaço restante ao lado da despensa */
  height: 100vh;
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
  padding: 20px 40px;
  box-sizing: border-box;
}

.header-section {
  text-align: center;
  margin-bottom: 30px;
}

.section-title {
  font-size: 14px;
  font-weight: 800;
  color: #374151;
  margin-bottom: 12px;
  letter-spacing: 0.05em;
}

.burger-name-input {
  display: inline-block;
  padding: 8px 24px;
  border: 1px solid #d1d5db;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
  color: #4b5563;
  background-color: #f9fafb;
}

.burger-stack-area {
  flex: 1;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow-y: auto;
  padding: 20px 0;
}

/* A linha central tracejada da imagem */
.center-guide-line {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 2px;
  background-image: linear-gradient(to bottom, #9ca3af 50%, transparent 50%);
  background-size: 2px 14px;
  background-repeat: repeat-y;
  z-index: 0;
}

.layers-container {
  width: 100%;
  max-width: 320px;
  z-index: 1;
}

.empty-state {
  text-align: center;
  color: #9ca3af;
  font-size: 14px;
  font-style: italic;
  background: white;
  padding: 20px;
  border-radius: 8px;
  border: 1px dashed #d1d5db;
}

.stack-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.layer-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: #ffffff;
  border: 2px solid #e5e7eb;
  border-radius: 12px;
  padding: 12px 16px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05);
  cursor: grab;
  transition: all 0.2s ease;
}

.layer-item:hover {
  border-color: #fb923c;
  transform: scale(1.02);
}

.layer-item:active {
  cursor: grabbing;
}

.layer-item.dragging {
  opacity: 0.4;
  border-style: dashed;
}

.drag-handle {
  color: #9ca3af;
  display: flex;
  align-items: center;
  justify-content: center;
}

.layer-content {
  flex: 1;
  text-align: center;
  display: flex;
  flex-direction: column;
}

.layer-text {
  font-weight: 700;
  color: #1f2937;
  font-size: 14px;
}

.layer-category {
  font-size: 10px;
  color: #6b7280;
  text-transform: uppercase;
  margin-top: 2px;
}

.icon {
  width: 20px;
  height: 20px;
}

.icon-trash {
  width: 18px;
  height: 18px;
}

.remove-btn {
  background: none;
  border: none;
  color: #ef4444;
  cursor: pointer;
  padding: 4px;
  border-radius: 4px;
  transition: background 0.2s;
}

.remove-btn:hover {
  background-color: #fee2e2;
}

/* Animações nativas do Vue para a lista ficar fluida ao trocar de lugar */
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(20px);
}
.list-move {
  transition: transform 0.3s ease;
}
</style>