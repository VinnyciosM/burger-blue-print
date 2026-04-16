<script setup>
import { ref } from 'vue'
import DeleteConfirmModal from './DeleteConfirmModal.vue'

const props = defineProps({
  projects: { type: Array, default: () => [] },
  activeId: { type: Number, default: null },
  isExpanded: { type: Boolean, default: false }
})

const emit = defineEmits(['create-new', 'select-project', 'toggle-expand', 'delete-project'])

// Estado para controlar o modal de exclusão
const projectToDelete = ref(null)

const confirmDelete = () => {
  if (projectToDelete.value) {
    emit('delete-project', projectToDelete.value.id)
    projectToDelete.value = null
  }
}
</script>

<template>
  <div class="manager-container">

    <div class="manager-title-container">
      <span class="manager-title">GERENCIADOR DE GUIAS</span>
      <button class="btn-toggle-manager" @click="$emit('toggle-expand')">
        <span>{{ isExpanded ? '⤡ REDUZIR GERENCIADOR DE GUIAS' : '⤢ EXPANDIR GERENCIADOR DE GUIAS' }}</span> 
      </button>
    </div>

    <div class="manager-scroll">
      <div class="manager-card add-card" @click="$emit('create-new')">
        <div class="card-content">
          <span class="plus-icon">+</span>
          <span class="card-label">Nova Guia</span>
        </div>
      </div>

      <div v-for="project in projects" :key="project.id" class="manager-card thumb-card"
        :class="{ 'is-editing': project.id === activeId }" @click="$emit('select-project', project, $emit('toggle-expand'))">
        
        <button class="btn-delete-guide" @click.stop="projectToDelete = project" title="Deletar Guia">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash" viewBox="0 0 16 16">
            <path d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0z"/>
            <path d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1zM4.118 4 4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4zM2.5 3h11V2h-11z"/>
          </svg>
        </button>

        <div v-if="project.id === activeId" class="status-badge">EDITANDO</div>

        <div class="project-header">
          <span class="project-name">{{ project.name || 'Sem Nome' }}</span>
        </div>

        <div class="mini-sheet">
          <div v-for="(col, idx) in project.data" :key="idx" class="mini-col"
            :class="{ 'active-col': col.layers.length > 0 }">

            <div class="mini-layers-stack" v-if="col.layers.length > 0">
              <img v-for="layer in col.layers" :key="layer.instanceId" :src="layer.image" class="mini-ingredient-img" />
            </div>

          </div>
        </div>
        <span class="project-id-tag">#{{ project.id }}</span>
      </div>
    </div>

    <DeleteConfirmModal 
      v-if="projectToDelete" 
      :item-name="projectToDelete.name" 
      @confirm="confirmDelete"
      @cancel="projectToDelete = null" 
    />
  </div>
</template>

<style scoped>
.manager-title-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 16px;
  padding-bottom: 10px;
  margin-top: 16px;
}

.manager-title{
  margin: 0;
  font-weight: 900;
  color: #374151;
}

.btn-toggle-manager {
  background: #f9fafb;
  color: #6b7280;
  border: 2px solid #d1d5db;
  border-radius: 6px;
  font-weight: 800;
  cursor: pointer;
  text-transform: uppercase;
  transition: all 0.2s;
  padding: 2px 14px;
}

.btn-toggle-manager:hover {
  background: #e5e7eb;
  color: #374151;
  border-color: #9ca3af;
}

.manager-container {
  display: flex;
  flex-direction: column;
  min-height: 0;
}

.manager-scroll {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 15px;
  overflow-y: auto;
  overflow-x: hidden;
  height: 100%;
  padding-bottom: 16px;
  padding-right: 16px;
}

.manager-card {
  width: 100%;
  height: 225px;
  background: white;
  border-radius: 8px;
  border: 1px solid #d1d5db;
  display: flex;
  flex-direction: column;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: all 0.2s ease;
}

.is-editing {
  border: 2px solid #fb923c !important;
  background: #fffaf7;
  box-shadow: 0 4px 12px rgba(251, 146, 60, 0.2);
}

.btn-delete-guide {
  position: absolute;
  top: 8px;
  right: 8px; 
  width: 30px;
  height: 30px;
  background: #ef4444;
  color: white;
  border: none;
  border-radius: 50%;
  font-size: 12px;
  font-weight: bold;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0; 
  transition: opacity 0.2s, transform 0.2s;
  z-index: 10;
}

.btn-delete-guide:hover {
  transform: scale(1.1);
  background: #dc2626;
}

.manager-card:hover .btn-delete-guide {
  opacity: 1; /* Aparece ao passar o mouse sobre o card */
}

/* O resto do seu CSS continua igual */
.status-badge {
  position: absolute;
  top: 0;
  right: 0;
  background: #fb923c;
  color: white;
  font-size: 8px;
  font-weight: 900;
  padding: 4px 8px;
  border-bottom-left-radius: 6px;
  z-index: 5;
}

.add-card {
  align-items: center;
  justify-content: center;
  border: 2px dashed #9ca3af;
}

.card-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: #6b7280;
  gap: 5px;
}

.plus-icon {
  font-size: 32px;
  color: #6b7280;
}

.card-label {
  font-size: 12px;
  font-weight: 700;
  color: #6b7280;
}

.project-header {
  padding: 6px 10px;
  background: rgba(0, 0, 0, 0.02);
  border-bottom: 1px solid #eee;
}

.project-name {
  font-size: 10px;
  font-weight: 900;
  color: #111827;
  text-transform: uppercase;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: block;
}

.project-id-tag {
  position: absolute;
  bottom: 4px;
  right: 8px;
  font-size: 9px;
  font-weight: 900;
  color: #d1d5db;
}

.mini-sheet {
  flex: 1;
  display: flex;
  border: 1px solid #f3f4f6;
  margin: 10px;
  background: white;
  overflow: hidden;
}

.mini-col {
  flex: 1;
  border-right: 1px solid #f3f4f6;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.mini-col:last-child {
  border-right: none;
}

.mini-col.active-col {
  background: #fffaf7;
}

.mini-layers-stack {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  padding-top: 6px;
}

.mini-ingredient-img {
  width: 85%;
  height: auto;
  max-height: 26px;
  object-fit: contain;
  mix-blend-mode: multiply;
  margin-top: -8px;
}

.mini-ingredient-img:first-child {
  margin-top: 0;
}

.manager-scroll::-webkit-scrollbar { width: 6px; }
.manager-scroll::-webkit-scrollbar-track { background: #f3f4f6; border-radius: 10px; }
.manager-scroll::-webkit-scrollbar-thumb { background: #d1d5db; border-radius: 10px; }
.manager-scroll::-webkit-scrollbar-thumb:hover { background: #9ca3af; }
</style>