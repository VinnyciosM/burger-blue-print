<script setup>
import { ref } from 'vue'

const props = defineProps({
  layers: {
    type: Array,
    required: true
  },
  modelValue: { // Este será o nome do hambúrguer
    type: String,
    default: ''
  }
})

const emit = defineEmits(['update:layers', 'update:modelValue'])

// --- LÓGICA DE DRAG AND DROP (MANTIDA) ---
const draggedIndex = ref(null)
const dragOverIndex = ref(null)

const onDragStart = (index, event) => {
  draggedIndex.value = index
  event.dataTransfer.effectAllowed = 'move'
}

const onDragOver = (index) => {
  dragOverIndex.value = index
}

const onDrop = (dropIndex) => {
  if (draggedIndex.value === null || draggedIndex.value === dropIndex) {
    clearDrag()
    return
  }
  const newLayers = [...props.layers]
  const draggedItem = newLayers.splice(draggedIndex.value, 1)[0]
  newLayers.splice(dropIndex, 0, draggedItem)
  emit('update:layers', newLayers)
  clearDrag()
}

const clearDrag = () => {
  draggedIndex.value = null
  dragOverIndex.value = null
}

const removeLayer = (index) => {
  const newLayers = [...props.layers]
  newLayers.splice(index, 1)
  emit('update:layers', newLayers)
}
</script>

<template>
  <main class="builder-column">
    <div class="builder-header">
      <div class="input-group">
        <label class="input-label">NOME DO PRODUTO</label>
        <input 
          type="text" 
          :value="modelValue" 
          @input="$emit('update:modelValue', $event.target.value)"
          placeholder="Ex: Drakon Smash..." 
          class="burger-name-input"
        />
      </div>
      
      <div class="meta-info">
        <h2 class="section-title">MONTAGEM</h2>
        <span class="layer-count">{{ layers.length }} ITENS</span>
      </div>
    </div>

    <div class="layers-list">
      <div v-if="layers.length === 0" class="empty-state">
        Arraste ou clique nos ingredientes para iniciar a montagem
      </div>

      <div 
        v-for="(layer, index) in layers" 
        :key="layer.instanceId" 
        class="layer-card"
        :class="{ 
          'is-dragging': draggedIndex === index,
          'is-dragover': dragOverIndex === index && draggedIndex !== index
        }"
        draggable="true"
        @dragstart="onDragStart(index, $event)"
        @dragover.prevent="onDragOver(index)"
        @drop="onDrop(index)"
        @dragend="clearDrag"
      >
        <div class="info-area">
          <span class="index">#{{ index + 1 }}</span>
          <span class="name">{{ layer.name }}</span>
        </div>

        <img :src="layer.image" :alt="layer.name" class="ingredient-img" />

        <button @click="removeLayer(index)" class="btn-delete-full" title="Remover">✕</button>
      </div>
    </div>
  </main>
</template>

<style scoped>
.builder-column {
  flex: 1;
  background-color: #fff;
  height: 100vh;
  display: flex;
  flex-direction: column;
  padding: 0; /* Removido padding para o header ocupar tudo */
  border-right: 1px solid #eee;
}

/* --- ESTILO DO NOVO HEADER --- */
.builder-header {
  padding: 20px;
  background: #fcfcfc;
  border-bottom: 1px solid #f3f4f6;
}

.input-group {
  margin-bottom: 15px;
}

.input-label {
  display: block;
  font-size: 9px;
  font-weight: 900;
  color: #9ca3af;
  margin-bottom: 4px;
  letter-spacing: 0.5px;
}

.burger-name-input {
  width: 100%;
  border: 1px solid #e5e7eb;
  border-radius: 6px;
  padding: 10px 12px;
  font-size: 14px;
  font-weight: 700;
  color: #1f2937;
  outline: none;
  transition: border-color 0.2s;
  background: white;
}

.burger-name-input:focus {
  border-color: #fb923c;
}

.meta-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.section-title { font-size: 12px; font-weight: 900; color: #111827; margin: 0; }
.layer-count { font-size: 10px; font-weight: 800; color: #fb923c; background: #fff7ed; padding: 2px 8px; border-radius: 20px; }

/* --- LISTA E CARDS (MANTIDOS) --- */
.layers-list { 
  flex: 1; 
  overflow-y: auto; 
  padding: 15px 20px; 
}

.layer-card {
  background: #ffffff;
  border: 1px solid #f3f4f6;
  border-radius: 8px;
  margin-bottom: 5px;
  display: flex;
  align-items: center;
  padding: 0 0 0 12px; 
  height: 48px;
  transition: all 0.2s;
  cursor: grab;
  overflow: hidden;
}

.layer-card:hover { border-color: #fb923c; }
.is-dragging { opacity: 0.3; border-style: dashed; }
.is-dragover { border-top: 2px solid #fb923c; margin-top: 10px; }

.info-area { flex: 1; display: flex; align-items: center; gap: 8px; }
.index { font-size: 9px; font-weight: 800; color: #fb923c; min-width: 18px; }
.name { font-size: 10px; font-weight: 800; color: #374151; text-transform: uppercase; }

.ingredient-img {
  width: 32px;
  height: 32px;
  object-fit: contain;
  margin-right: 10px;
  mix-blend-mode: multiply;
}

.btn-delete-full {
  width: 45px;
  height: 100%;
  border: none;
  background: #f9fafb;
  border-left: 1px solid #f3f4f6;
  color: #d1d5db;
  font-size: 14px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.btn-delete-full:hover { background: #fee2e2; color: #ef4444; }

.empty-state { text-align: center; color: #d1d5db; font-size: 11px; margin-top: 40px; padding: 0 20px; line-height: 1.5; }
</style>