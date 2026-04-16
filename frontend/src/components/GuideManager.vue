<script setup>
const props = defineProps({
  projects: { type: Array, default: () => [] },
  activeId: { type: Number, default: null }
})
defineEmits(['create-new', 'select-project'])
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

      <div 
        v-for="project in projects" 
        :key="project.id" 
        class="manager-card thumb-card"
        :class="{ 'is-editing': project.id === activeId }"
        @click="$emit('select-project', project)"
      >
        <div v-if="project.id === activeId" class="edit-badge">EDITANDO</div>

        <div class="project-header">
          <span class="project-name">{{ project.name || 'Sem Nome' }}</span>
        </div>

        <div class="mini-sheet">
          <div class="mini-col"></div>
          <div class="mini-col active-col">
            <div class="mini-dot" v-for="i in 4" :key="i"></div>
          </div>
          <div class="mini-col"></div>
        </div>
        <span class="project-id-tag">#{{ project.id }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.manager-container { display: flex; flex-direction: column; gap: 12px; }
.manager-title { font-size: 11px; font-weight: 900; color: #1f2937; text-transform: uppercase; margin: 0; }
.manager-scroll { display: flex; gap: 15px; overflow-x: auto; padding: 5px 0 10px 0; }

.manager-card {
  flex: 0 0 160px; aspect-ratio: 4 / 3; background: white; border-radius: 8px; border: 1px solid #d1d5db;
  display: flex; flex-direction: column; cursor: pointer; position: relative; overflow: hidden;
  transition: all 0.2s ease;
}

/* DESTAQUE DO ATUAL */
.is-editing { 
  border: 2px solid #fb923c !important; 
  background: #fffaf7; 
  box-shadow: 0 4px 12px rgba(251, 146, 60, 0.2);
}

.edit-badge {
  position: absolute; top: 0; right: 0; background: #fb923c; color: white;
  font-size: 6px; font-weight: 900; padding: 2px 6px; border-bottom-left-radius: 6px; z-index: 5;
}

/* Visual Minimalista (Sem Thumbnail) */
.project-header { padding: 4px 8px; background: rgba(0,0,0,0.02); border-bottom: 1px solid #eee; }
.project-name { font-size: 7px; font-weight: 900; color: #111827; text-transform: uppercase; }

.mini-sheet { flex: 1; display: flex; border: 1px solid #f3f4f6; margin: 8px; }
.mini-col { flex: 1; border-right: 1px solid #f3f4f6; }
.mini-col.active-col { background: #fffaf7; display: flex; flex-direction: column; align-items: center; gap: 3px; padding-top: 5px; }
.mini-dot { width: 60%; height: 2px; background: #fb923c; border-radius: 1px; }

.add-card { align-items: center; justify-content: center; border: 2px dashed #9ca3af; }
.plus-icon { font-size: 24px; color: #6b7280; }
.card-label { font-size: 10px; font-weight: 700; color: #6b7280; }
.project-id-tag { position: absolute; bottom: 2px; right: 5px; font-size: 7px; font-weight: 900; color: #d1d5db; }

.manager-scroll::-webkit-scrollbar { height: 4px; }
.manager-scroll::-webkit-scrollbar-thumb { background: #d1d5db; border-radius: 10px; }
</style>