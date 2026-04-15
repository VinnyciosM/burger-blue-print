<script setup>
const props = defineProps({
  projects: { type: Array, default: () => [] },      // Histórico (LocalStorage)
  currentGuides: { type: Array, required: true }      // O que está no visualizador agora
})

const emit = defineEmits(['create-new', 'select-project'])
</script>

<template>
  <div class="manager-container">
    <h2 class="manager-title">GERENCIADOR DE GUIAS</h2>

    <div class="manager-scroll">
      <div class="manager-card add-card" @click="$emit('create-new')">
        <div class="card-content">
          <span class="plus-icon">+</span>
          <span class="card-label">Nova Guia</span>
        </div>
      </div>

      <div class="manager-card thumb-card active-project">
        <div class="edit-status-badge">EDITANDO</div>
        
        <div class="project-header">
          <span class="project-name">{{ currentGuides[0].name || currentGuides[1].name || currentGuides[2].name || 'Guia em Branco' }}</span>
        </div>

        <div class="mini-sheet">
          <div v-for="(col, idx) in currentGuides" :key="idx" class="mini-col">
            <div class="mini-layers-stack">
              <img v-for="layer in col.layers.slice(0, 4)" :key="layer.instanceId" :src="layer.image" class="mini-ingredient" />
            </div>
          </div>
        </div>
      </div>

      <div 
        v-for="project in projects" 
        :key="project.id" 
        class="manager-card thumb-card"
        @click="$emit('select-project', project)"
      >
        <div class="project-header">
          <span class="project-name">{{ project.name || 'Projeto sem Nome' }}</span>
        </div>

        <div class="mini-sheet">
          <div v-for="(col, idx) in project.data" :key="idx" class="mini-col">
            <div class="mini-layers-stack">
              <img v-for="layer in col.layers.slice(0, 4)" :key="layer.instanceId" :src="layer.image" class="mini-ingredient" />
            </div>
          </div>
        </div>
        <span class="project-id-tag">#{{ project.id }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.manager-container { display: flex; flex-direction: column; gap: 12px; }
.manager-title { font-size: 11px; font-weight: 900; color: #1f2937; text-transform: uppercase; margin: 0; }
.manager-scroll { display: flex; gap: 15px; overflow-x: auto; padding-bottom: 10px; }

.manager-card {
  flex: 0 0 160px; aspect-ratio: 4 / 3; background: white; border-radius: 8px; border: 1px solid #d1d5db;
  display: flex; flex-direction: column; cursor: pointer; position: relative; overflow: hidden;
}

/* Destaque para o projeto que está na tela */
.active-project { border: 2px solid #fb923c; background: #fffaf7; box-shadow: 0 4px 12px rgba(251, 146, 60, 0.2); }

.edit-status-badge {
  position: absolute; top: 0; right: 0; background: #fb923c; color: white;
  font-size: 6px; font-weight: 900; padding: 2px 6px; border-bottom-left-radius: 6px; z-index: 5;
}

.add-card { align-items: center; justify-content: center; border: 2px dashed #9ca3af; }
.thumb-card { padding: 5px; border-color: #e5e7eb; }

.project-header { padding: 2px 5px; background: rgba(0,0,0,0.03); border-bottom: 1px solid #eee; margin-bottom: 4px; }
.project-name { font-size: 7px; font-weight: 900; color: #111827; text-transform: uppercase; display: block; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

.mini-sheet { flex: 1; display: flex; background: #fff; border: 1px solid #f3f4f6; }
.mini-col { flex: 1; border-right: 1px solid #f3f4f6; display: flex; flex-direction: column; align-items: center; padding-top: 2px; }
.mini-col:last-child { border-right: none; }

.mini-layers-stack { display: flex; flex-direction: column; gap: 1px; width: 100%; align-items: center; }
.mini-ingredient { width: 85%; height: auto; object-fit: contain; mix-blend-mode: multiply; }

.plus-icon { font-size: 24px; color: #6b7280; }
.card-label { font-size: 10px; font-weight: 700; color: #6b7280; }
.project-id-tag { position: absolute; bottom: 2px; right: 5px; font-size: 7px; font-weight: 900; color: #d1d5db; }

.manager-scroll::-webkit-scrollbar { height: 4px; }
.manager-scroll::-webkit-scrollbar-thumb { background: #d1d5db; border-radius: 10px; }
</style>