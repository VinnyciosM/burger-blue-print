<script setup>
import { computed } from 'vue'

const props = defineProps({
  // Array containing the data for all 3 guides
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

/**
 * Calculates height and gap based on layer count to keep everything within the A4 scale
 * @param {Array} layers - The array of ingredients
 */
const getStackStyle = (layers) => {
  const total = layers.length
  if (total === 0) return { height: '80px', gap: '10px' }

  // Virtual A4 height for calculation
  const AVAILABLE_HEIGHT = 480 
  const calculatedHeight = (AVAILABLE_HEIGHT / total)
  
  // Max height increased to 140px to allow larger images when few items exist
  const finalHeight = Math.min(140, Math.max(10, calculatedHeight))
  // Gap is zeroed out sooner to prioritize image size
  const finalGap = total > 7 ? '0px' : '10px'

  return {
    height: finalHeight + 'px',
    gap: finalGap
  }
}
</script>

<template>
  <aside class="action-sidebar">
    
    <div class="placeholder-space">
      FREE AREA <br> 
      (Click a guide to edit)
    </div>

    <div class="a4-wrapper">
      <div class="a4-sheet">
        
        <div 
          v-for="(guide, index) in guides" 
          :key="index"
          class="guide-column"
          :class="{ 'active-guide': activeIndex === index }"
          @click="$emit('update:activeIndex', index)"
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

            <div v-if="guide.layers.length === 0" class="empty-state">
              EMPTY SLOT
            </div>
          </div>
        </div>

      </div>
    </div>

  </aside>
</template>

<style scoped>
/* Main sidebar container - Padding 20px creates the safe margin around the screen edges */
.action-sidebar {
  flex: 1;
  height: 100vh;
  background-color: #f3f4f6;
  display: flex;
  flex-direction: column;
  padding: 20px; 
  box-sizing: border-box;
  overflow: hidden;
}

/* Free area container - Now takes all available vertical space */
.placeholder-space {
  flex: 1; /* MAGIC: Pushes everything below it to the bottom of the screen */
  border: 2px dashed #d1d5db;
  border-radius: 12px;
  
  /* Creates the 20px gap between this free area and the A4 sheet */
  margin-bottom: 20px; 
  
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  color: #9ca3af;
  font-size: 11px;
  font-weight: 800;
  min-height: 100px; /* Prevents it from disappearing on very short screens */
}

/* Wrapper for the A4 sheet - No longer expands, just hugs the bottom */
.a4-wrapper {
  display: flex;
  justify-content: center;
  width: 100%;
}

/* Simulated A4 landscape sheet (297x210mm) */
.a4-sheet {
  background: white;
  aspect-ratio: 297 / 210;
  width: 100%;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  display: flex;
}

/* Base style for the 3 columns */
.guide-column {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 20px 8px;
  cursor: pointer;
  position: relative;
  align-items: flex-start;
}

.guide-column:not(:last-child) {
  border-right: 1px dashed #e5e7eb;
}

/* Highlight the guide being edited */
.active-guide {
  background-color: #fffaf7;
  box-shadow: inset 0 0 0 2px #fb923c;
  z-index: 2;
}

/* Visual Indicator of Active Selection */
.selection-indicator {
  position: absolute;
  top: 5px;
  right: 10px;
  font-size: 7px;
  font-weight: 900;
  color: #fb923c;
  letter-spacing: 1px;
}

/* Product name title */
.preview-label { 
  font-size: 10px; 
  font-weight: 900; 
  color: #111827; 
  margin-bottom: 20px; 
  text-transform: uppercase;
  border-bottom: 2px solid #fb923c;
  padding-bottom: 6px;
  width: 85%;
  align-self: center; 
  text-align: center;
}

/* Main stack container */
.burger-stack {
  width: 100%;
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  overflow: hidden;
}

/* Single layer row */
.stack-item {
  display: flex;
  align-items: center;
  width: 100%;
  min-height: 0;
  max-width: 100%; 
}

/* Fixed image container to align tags perfectly */
.ingredient-img {
  width: 80px; 
  object-fit: contain;
  mix-blend-mode: multiply;
  flex-shrink: 0;
}

/* Technical label (Line + Text) */
.guide-tag {
  display: flex;
  align-items: center;
  margin-left: 4px;
  flex: 1; 
  min-width: 0;
}

.line { 
  width: 10px; 
  height: 1px; 
  background: #d1d5db; 
  margin-right: 4px;
  flex-shrink: 0;
}

.name { 
  font-size: 7px; 
  font-weight: 800; 
  color: #6b7280; 
  text-transform: uppercase; 
  line-height: 1.1;
  word-break: break-word;
  white-space: normal;
}

.empty-state { 
  margin: 50px auto 0;
  color: #d1d5db; 
  font-size: 8px; 
  font-weight: 900; 
}
</style>