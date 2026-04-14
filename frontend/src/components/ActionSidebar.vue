<script setup>
import { computed, ref, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  layers: {
    type: Array,
    required: true
  },
  name: {
    type: String,
    default: ''
  }
})

const windowHeight = ref(window.innerHeight)
const updateHeight = () => { windowHeight.value = window.innerHeight }

onMounted(() => window.addEventListener('resize', updateHeight))
onUnmounted(() => window.removeEventListener('resize', updateHeight))

const dynamicStyle = computed(() => {
  const total = props.layers.length
  if (total === 0) return { height: '150px', gap: '10px' }

  // O hambúrguer continua ocupando no máximo 60% da tela, 
  // deixando o resto do espaço para a nossa div vazia e paddings.
  const AVAILABLE_HEIGHT = windowHeight.value * 0.6
  
  const calculatedHeight = (AVAILABLE_HEIGHT / total)
  const finalHeight = Math.min(200, Math.max(10, calculatedHeight))
  const finalGap = total > 10 ? '0px' : '12px'

  return {
    height: finalHeight + 'px',
    gap: finalGap
  }
})
</script>

<template>
  <aside class="action-sidebar">
    
    <div class="placeholder-space">
      ÁREA LIVRE <br> 
      (Para Logo, Botões de Salvar/Imprimir ou Infos da Receita)
    </div>

    <div class="visualizer-container">
      <h3 class="preview-label">
        {{ name.trim() !== '' ? name : 'NOVO PRODUTO' }}
      </h3>

      <div class="burger-stack" :style="{ gap: dynamicStyle.gap }">
        <div 
          v-for="layer in layers" 
          :key="layer.instanceId" 
          class="stack-item"
          :style="{ height: dynamicStyle.height }"
        >
          <img 
            :src="layer.image" 
            class="ingredient-img" 
            :style="{ height: '100%' }" 
          />
          
          <div class="guide-tag">
            <div class="line"></div>
            <span class="name">{{ layer.name }}</span>
          </div>
        </div>

        <div v-if="layers.length === 0" class="empty-state">
          AGUARDANDO MONTAGEM...
        </div>
      </div>
    </div>
  </aside>
</template>

<style scoped>
.action-sidebar {
  width: 35%;
  height: 100vh;
  background-color: #f3f4f6;
  display: flex;
  flex-direction: column;
  padding: 20px;
  box-sizing: border-box;
  overflow: hidden;
}

/* --- ESTILO DO ESPAÇO VAZIO --- */
.placeholder-space {
  min-height: 100px; /* Define uma altura inicial para você ter noção do espaço */
  border: 2px dashed #d1d5db;
  border-radius: 12px;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  color: #9ca3af;
  font-size: 11px;
  font-weight: 800;
  background-color: transparent;
  flex-shrink: 0; /* Impede que esse espaço seja esmagado pelo hambúrguer */
}

.visualizer-container {
  flex: 1;
  background: white;
  border-radius: 16px;
  border: 1px solid #e5e7eb;
  padding: 30px 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow: hidden;
}

.preview-label { 
  font-size: 16px; 
  font-weight: 900; 
  color: #111827; 
  margin-bottom: 30px; 
  text-transform: uppercase;
  text-align: center;
  border-bottom: 3px solid #fb923c;
  padding-bottom: 8px;
  min-width: 150px;
}

.burger-stack {
  width: 100%;
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  overflow: hidden;
}

.stack-item {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  width: 100%;
  flex-shrink: 1; 
  min-height: 0;
}

.ingredient-img {
  width: auto;
  max-width: 100%;
  object-fit: contain;
  mix-blend-mode: multiply;
}

.guide-tag {
  position: absolute;
  left: calc(50% + 90px);
  display: flex;
  align-items: center;
  pointer-events: none;
}

.line { width: 30px; height: 1px; background: #d1d5db; margin-right: 10px; }
.name { font-size: 9px; font-weight: 800; color: #6b7280; text-transform: uppercase; white-space: nowrap; }

.empty-state { 
  margin-top: 100px; 
  color: #d1d5db; 
  font-size: 12px; 
  font-weight: 900; 
}
</style>