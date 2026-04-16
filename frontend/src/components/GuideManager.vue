<script setup>
const props = defineProps({
  projects: { type: Array, default: () => [] },
  activeId: { type: Number, default: null } // Recebe o ID ativo do App.vue
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

      <div v-for="project in projects" :key="project.id" class="manager-card thumb-card"
        :class="{ 'is-editing': project.id === activeId }" @click="$emit('select-project', project)">
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
  </div>
</template>

<style scoped>
.manager-container {
  display: flex;
  flex-direction: column;
  min-height: 0;
}

.manager-scroll {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  
  overflow-y: auto;
  overflow-x: hidden;
  height: 100%;
  padding-bottom: 16px;
  padding-right: 16px; 
}

.manager-card {
  width: 100%; 
  height: 200px; 
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


.manager-title {
  font-size: 13px;
  font-weight: 900;
  color: #1f2937;
  text-transform: uppercase;
  margin: 0;
  padding-bottom: 16px;
}

.is-editing {
  border: 2px solid #fb923c !important;
  background: #fffaf7;
  box-shadow: 0 4px 12px rgba(251, 146, 60, 0.2);
}

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

/* Barra de rolagem (Scrollbar) Vertical Customizada */
.manager-scroll::-webkit-scrollbar {
  width: 6px;
}

.manager-scroll::-webkit-scrollbar-track {
  background: #f3f4f6;
  border-radius: 10px;
}

.manager-scroll::-webkit-scrollbar-thumb {
  background: #d1d5db;
  border-radius: 10px;
}

.manager-scroll::-webkit-scrollbar-thumb:hover {
  background: #9ca3af;
}
</style>