<script setup>
import { ref } from 'vue'
import html2canvas from 'html2canvas'
import GuideManager from './GuideManager.vue'

const props = defineProps({
  guides: { type: Array, required: true },
  activeIndex: { type: Number, default: 0 },
  savedProjects: { type: Array, default: () => [] }
})

// Adicionado 'load-project' para permitir o carregamento
const emit = defineEmits(['update:activeIndex', 'new-project', 'load-project'])
const isPreviewMode = ref(false)

const togglePreview = () => isPreviewMode.value = !isPreviewMode.value

const exportAsImage = () => {
  const element = document.getElementById('printable-a4')
  element.classList.add('clean')

  html2canvas(element, {
    allowTaint: true,
    useCORS: true,
    backgroundColor: '#ffffff',
    scale: 3
  }).then((canvas) => {
    const link = document.createElement('a')
    link.download = `guia-drakon-final.png`
    link.href = canvas.toDataURL("image/png")
    link.click()
    element.classList.remove('clean')
  }).catch(() => element.classList.remove('clean'))
}
</script>

<template>
  <aside class="sidebar">
    <!--     <div class="header">
      <span class="label">FINALIZAÇÃO</span>
      <button class="btn-primary" @click="togglePreview">MODO PREVIEW</button>
    </div> -->

    <div class="canvas-area" :class="{ 'fullscreen': isPreviewMode }">
      <div v-if="isPreviewMode" class="overlay" @click="togglePreview"></div>

      <div v-if="isPreviewMode" class="toolbar">
        <button class="btn-save" @click="exportAsImage">BAIXAR IMAGEM</button>
        <button class="btn-exit" @click="togglePreview">FECHAR</button>
      </div>

      <div class="a4-sheet" :class="{ 'clean': isPreviewMode }" id="printable-a4">
        <div v-for="(guide, index) in guides" :key="index" class="column"
          :class="{ 'active': activeIndex === index && !isPreviewMode }"
          @click="!isPreviewMode && $emit('update:activeIndex', index)">
          <div v-if="!isPreviewMode && activeIndex === index" class="edit-badge">
            EDITANDO
          </div>

          <h3 class="guide-title">{{ guide.name || `GUIA ${index + 1}` }}</h3>

          <div class="layers-list">
            <div v-for="layer in guide.layers" :key="layer.instanceId" class="layer-item">

              <img :src="layer.image" class="ingredient-img" />

              <div class="info">
                <span class="layer-name">{{ layer.name }}</span>
              </div>

            </div>
            <div v-if="guide.layers.length === 0" class="empty-msg">VAZIO</div>
          </div>
        </div>
      </div>
    </div>

    <button class="btn-preview-blue" @click="togglePreview">
      PREVIEW
    </button>

   <GuideManager 
      :projects="savedProjects" 
      :current-guides="guides"
      @create-new="$emit('new-project')" 
      @select-project="$emit('load-project', $event)" 
    />

  </aside>
</template>

<style scoped>
.btn-preview-blue {
  width: 100%;
  padding: 12px;
  background: #4a90e2;
  color: white;
  border: none;
  border-radius: 6px;
  font-weight: 900;
  font-size: 14px;
  cursor: pointer;
  letter-spacing: 1px;
  margin-top: 10px;
  margin-bottom: 16px;
}


.sidebar {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 20px;
  background: #f3f4f6;
  overflow: hidden;
}

.header {
  padding: 20px;
  border: 2px dashed #d1d5db;
  border-radius: 12px;
  margin-bottom: 20px;
  text-align: center;
}

.label {
  display: block;
  font-size: 10px;
  font-weight: 900;
  color: #9ca3af;
  letter-spacing: 2px;
  margin-bottom: 15px;
}

button {
  font-weight: 900;
  cursor: pointer;
  border: none;
  border-radius: 6px;
  transition: background 0.2s;
}

.btn-primary {
  background: #3b82f6;
  color: #fff;
  padding: 12px 24px;
}

.btn-save {
  background: #fb923c;
  color: #fff;
  padding: 10px 20px;
}

.btn-exit {
  background: rgba(255, 255, 255, 0.1);
  color: #fff;
  padding: 10px 20px;
}

.btn-exit:hover {
  background: #ef4444;
}

.a4-sheet {
  background: #fff;
  aspect-ratio: 297/210;
  width: 100%;
  display: flex;
  position: relative;
  z-index: 10;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.column {
  flex: 1;
  padding: 15px 8px;
  display: flex;
  flex-direction: column;
  border-right: 1px dashed #e5e7eb;
  position: relative;
  cursor: pointer;
}

.column:last-child {
  border: none;
}

.active {
  background: #fffaf7;
  box-shadow: inset 0 0 0 2px #fb923c;
  z-index: 2;
}

.edit-badge {
  position: absolute;
  top: 8px;
  right: 10px;
  font-size: 7px;
  font-weight: 900;
  color: #fb923c;
}

.guide-title {
  font-size: 11px;
  font-weight: 900;
  text-align: center;
  border-bottom: 2px solid #fb923c;
  padding-bottom: 8px;
  margin-bottom: 15px;
  text-transform: uppercase;
  color: #111827;
}

/* --- ESTRUTURA UNIFICADA (Side-by-Side) --- */
.layers-list {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 4px;
  min-height: 0;
}

.layer-item {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  /* Imagem na esquerda, texto na direita */
  min-height: 0;
  max-height: 150px;
  width: 100%;
}

/* Imagem Base (Visualizador Reduzido) */
.ingredient-img {
  max-width: 120px;
  /* CONSTRAINT: Limite menor para caber na tela de edição */
  height: 100%;
  width: auto;
  object-fit: contain;
  mix-blend-mode: multiply;
}

/* Texto Base (Ambos os modos) */
.info {
  display: flex;
  align-items: center;
  padding-left: 8px;
  flex: 1;
  min-width: 0;
}

.layer-name {
  font-size: 7px;
  font-weight: 800;
  color: #4b5563;
  text-transform: uppercase;
  line-height: 1.1;
  white-space: normal;
  word-wrap: break-word;
}

/* --- MODO PREVIEW / FULLSCREEN (Escala Aumentada) --- */
.fullscreen .a4-sheet {
  width: auto;
  height: 98vh;
  z-index: 20;
}

.fullscreen .column {
  padding: 10px 8px;
}

.fullscreen .guide-title {
  font-size: 14px;
  margin-bottom: 15px;
  padding-bottom: 10px;
}

.fullscreen .layers-list {
  gap: 2px;
}

/* Escala vertical maior no Fullscreen */
.fullscreen .layer-item {
  max-height: 250px;
}

/* Escala horizontal maior no Fullscreen */
.fullscreen .ingredient-img {
  max-width: 290px !important;
  /* CONSTRAINT: Limite de 290px conforme solicitado */
}

/* Ajustes de tipografia e espaçamento para o Fullscreen */
.fullscreen .info {
  padding-left: 12px;
}

.fullscreen .layer-name {
  font-size: 9px;
}

/* Fullscreen Overlays */
.fullscreen {
  position: fixed;
  inset: 0;
  padding: 5px;
  z-index: 999;
}

.overlay {
  position: absolute;
  inset: 0;
  background: rgba(17, 24, 39, 0.98);
  z-index: 5;
}

.toolbar {
  position: absolute;
  top: 20px;
  right: 30px;
  display: flex;
  gap: 10px;
  z-index: 1000;
}

/* Limpeza PNG */
.clean .active {
  background: none !important;
  box-shadow: none !important;
  border-right: 1px dashed #e5e7eb !important;
}

.empty-msg {
  margin: auto;
  font-size: 9px;
  font-weight: 900;
  color: #d1d5db;
}
</style>