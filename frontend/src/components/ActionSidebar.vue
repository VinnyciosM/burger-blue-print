<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  guides: {
    type: Array,
    required: true
  },
  activeIndex: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(['update:activeIndex'])

// State to control fullscreen preview
const isPreviewMode = ref(false)

// Toggle function for the preview mode
const togglePreview = () => {
  isPreviewMode.value = !isPreviewMode.value
}

/**
 * Dynamic calculation for image scaling
 */
const getStackStyle = (layers) => {
  const total = layers.length
  if (total === 0) return { height: '80px', gap: '10px' }

  const AVAILABLE_HEIGHT = 480 
  const calculatedHeight = (AVAILABLE_HEIGHT / total)
  
  const finalHeight = Math.min(140, Math.max(10, calculatedHeight))
  const finalGap = total > 7 ? '0px' : '10px'

  return { height: finalHeight + 'px', gap: finalGap }
}
</script>

<template>
  <aside class="action-sidebar">
    
    <div class="placeholder-space">
      <div class="action-card">
        <span class="action-title">FINALIZAÇÃO</span>
        <p class="action-desc">Verifique a folha A4 em tela cheia antes de salvar</p>
        
        <button class="btn-preview" @click="togglePreview">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path><circle cx="12" cy="12" r="3"></circle></svg>
          PRÉ-VISUALIZAR GUIA
        </button>
      </div>
    </div>

    <div class="a4-wrapper" :class="{ 'is-fullscreen': isPreviewMode }">
      
      <div class="preview-backdrop" v-if="isPreviewMode" @click="togglePreview"></div>
      
      <button class="btn-close" v-if="isPreviewMode" @click="togglePreview">
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
        FECHAR
      </button>

      <div class="a4-sheet" :class="{ 'clean-mode': isPreviewMode }">
        <div 
          v-for="(guide, index) in guides" 
          :key="index"
          class="guide-column"
          :class="{ 'active-guide': activeIndex === index }"
          @click="!isPreviewMode && $emit('update:activeIndex', index)"
        >
          <div class="selection-indicator" v-if="activeIndex === index">EDITING</div>

          <h3 class="preview-label">
            {{ guide.name.trim() !== '' ? guide.name : `GUIA ${index + 1}` }}
          </h3>

          <div class="burger-stack" :style="{ gap: getStackStyle(guide.layers).gap }">
            <div 
              v-for="layer in guide.layers" 
              :key="layer.instanceId" 
              class="stack-item"
              :style="{ height: getStackStyle(guide.layers).height }"
            >
              <img :src="layer.image" class="ingredient-img" :style="{ height: '100%' }" />
              <div class="guide-tag">
                <div class="line"></div>
                <span class="name">{{ layer.name }}</span>
              </div>
            </div>

            <div v-if="guide.layers.length === 0" class="empty-state">EMPTY SLOT</div>
          </div>
        </div>
      </div>
    </div>

  </aside>
</template>

<style scoped>
/* Main Layout */
.action-sidebar { flex: 1; height: 100vh; background-color: #f3f4f6; display: flex; flex-direction: column; padding: 20px; box-sizing: border-box; overflow: hidden; }

/* Control Card */
.placeholder-space { flex: 1; border: 2px dashed #d1d5db; border-radius: 12px; margin-bottom: 20px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.action-card { text-align: center; display: flex; flex-direction: column; align-items: center; }
.action-title { font-size: 10px; font-weight: 900; color: #9ca3af; letter-spacing: 2px; }
.action-desc { font-size: 12px; color: #4b5563; margin: 8px 0 20px 0; font-weight: 500; }

/* New Preview Button */
.btn-preview { background-color: #3b82f6; color: white; border: none; padding: 14px 24px; border-radius: 8px; font-size: 11px; font-weight: 900; cursor: pointer; display: flex; align-items: center; gap: 10px; box-shadow: 0 4px 6px -1px rgba(59, 130, 246, 0.2); }
.btn-preview:hover { background-color: #2563eb; transform: translateY(-2px); }

/* --- A4 SHEET BASE --- */
.a4-wrapper { display: flex; justify-content: center; width: 100%; }
.a4-sheet { background: white; aspect-ratio: 297 / 210; width: 100%; box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15); display: flex; z-index: 10001; }

.guide-column { flex: 1; display: flex; flex-direction: column; padding: 20px 8px; cursor: pointer; position: relative; align-items: flex-start; }
.guide-column:not(:last-child) { border-right: 1px dashed #e5e7eb; }

.active-guide { background-color: #fffaf7; box-shadow: inset 0 0 0 2px #fb923c; z-index: 2; }
.selection-indicator { position: absolute; top: 5px; right: 10px; font-size: 7px; font-weight: 900; color: #fb923c; }

.preview-label { font-size: 10px; font-weight: 900; color: #111827; margin-bottom: 20px; text-transform: uppercase; border-bottom: 2px solid #fb923c; padding-bottom: 6px; width: 85%; align-self: center; text-align: center; }

.burger-stack { width: 100%; flex: 1; display: flex; flex-direction: column; align-items: flex-start; overflow: hidden; }
.stack-item { display: flex; align-items: center; width: 100%; min-height: 0; }

.ingredient-img { width: 80px; object-fit: contain; mix-blend-mode: multiply; flex-shrink: 0; }
.guide-tag { display: flex; align-items: center; margin-left: 4px; flex: 1; min-width: 0; }
.line { width: 10px; height: 1px; background: #d1d5db; margin-right: 4px; flex-shrink: 0; }
.name { font-size: 7px; font-weight: 800; color: #6b7280; text-transform: uppercase; line-height: 1.1; word-break: break-word; white-space: normal; }
.empty-state { margin: 50px auto 0; color: #d1d5db; font-size: 8px; font-weight: 900; }


/* --- FULLSCREEN PREVIEW MODE --- */

.a4-wrapper.is-fullscreen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 9999;
  align-items: center;
  padding: 40px;
  box-sizing: border-box;
}

.preview-backdrop {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(17, 24, 39, 0.95); /* Deep dark background */
  cursor: pointer;
}

/* Close Button Floating on top right */
.btn-close {
  position: absolute;
  top: 30px;
  right: 40px;
  z-index: 10002;
  background: rgba(255, 255, 255, 0.1);
  color: white;
  border: 1px solid rgba(255, 255, 255, 0.2);
  padding: 10px 20px;
  border-radius: 8px;
  font-size: 11px;
  font-weight: 900;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 8px;
}

.btn-close:hover {
  background: #ef4444; /* Turns red on hover */
  border-color: #ef4444;
}

/* Scale the sheet up, respecting the aspect ratio and screen boundaries */
.is-fullscreen .a4-sheet {
  width: auto;
  max-width: 95vw;
  height: 90vh; /* Takes 90% of screen height */
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
  border-radius: 4px; /* Slight rounding for UI aesthetic */
}

/* UI Cleanup Rules for Preview (Replaces the PDF logic) */
.clean-mode .selection-indicator { display: none !important; }

.clean-mode .active-guide {
  background-color: transparent !important;
  box-shadow: none !important;
  border-right: 1px dashed #e5e7eb !important;
}

/* Disable pointer events to stop editing while in preview */
.clean-mode .guide-column { cursor: default; }



</style>