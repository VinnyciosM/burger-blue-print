<script setup>
import { ref } from 'vue'
import html2canvas from 'html2canvas'

const props = defineProps({
  guides: { type: Array, required: true },
  activeIndex: { type: Number, default: 0 }
})

const emit = defineEmits(['update:activeIndex'])
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
    link.download = `guia-drakon-full.png`
    link.href = canvas.toDataURL("image/png")
    link.click()
    element.classList.remove('clean')
  }).catch(() => element.classList.remove('clean'))
}
</script>

<template>
  <aside class="sidebar">
    <div class="header">
      <span class="label">FINALIZAÇÃO</span>
      <button class="btn-primary" @click="togglePreview">MODO PREVIEW</button>
    </div>

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
          <h3 class="guide-title">{{ guide.name || `GUIA ${index + 1}` }}</h3>

          <div class="layers-list">
            <div v-for="layer in guide.layers" :key="layer.instanceId" class="layer-item">
              <img :src="layer.image" class="ingredient-img" />
            </div>
            <div v-if="guide.layers.length === 0" class="empty-msg">VAZIO</div>
          </div>
        </div>
      </div>
    </div>
  </aside>
</template>

<style scoped>
/* Base */
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

/* Botões UI */
button {
  font-weight: 900;
  cursor: pointer;
  border: none;
  border-radius: 6px;
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

/* Visualizador */
.canvas-area {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 0;
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

/* Colunas (Visualizador Reduzido) */
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

/* Listagem Visual */
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
  justify-content: center;
  min-height: 0;
  max-height: 180px;
  width: 100%;
}

.ingredient-img {
  max-width: 98%;
  max-height: 100%;
  width: auto;
  height: auto;
  object-fit: contain;
  mix-blend-mode: multiply;
}

/* --- AJUSTES PARA O VISUALIZADOR COMPLETO (Aumentar Imagens) --- */
.fullscreen .a4-sheet {
  width: auto;
  height: 98vh;
  z-index: 20;
}

.fullscreen .column {
  padding: 10px 5px;
}

/* Menos margem = mais espaço para imagem */
.fullscreen .guide-title {
  font-size: 14px;
  margin-bottom: 10px;
  padding-bottom: 10px;
}

.fullscreen .layer-item {
  max-height: 250px;
}

/* Permite que a imagem cresça muito mais se houver espaço */
.fullscreen .layers-list {
  gap: 2px;
}

/* Fullscreen Overlay */
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