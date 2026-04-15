<template>
  <div class="manager-container">
    <h2 class="manager-title">GERENCIADOR DE GUIAS</h2>

    <div class="manager-scroll">
      <div class="manager-card add-card">
        <div class="card-content">
          <span class="plus-icon">+</span>
          <span class="card-label">Nova Guia</span>
        </div>
      </div>

      <div class="manager-card thumb-card" v-for="i in 2" :key="i">
        <div class="mini-sheet">
          <div class="mini-col"></div>
          <div class="mini-col active">
            <div class="mini-dot" v-for="j in 4" :key="j"></div>
          </div>
          <div class="mini-col"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.manager-container {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.manager-title {
  font-size: 11px;
  font-weight: 900;
  color: #1f2937;
  margin: 0;
  text-transform: uppercase;
}

.manager-scroll {
  display: flex;
  gap: 15px;
  overflow-x: auto;
  padding-bottom: 10px;
}

.manager-card {
  flex: 0 0 160px;
  aspect-ratio: 4 / 3;
  background: white;
  border-radius: 8px;
  border: 1px solid #d1d5db;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.add-card {
  border: 2px solid #9ca3af;
}

.card-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: #6b7280;
}

.plus-icon {
  font-size: 24px;
}

.card-label {
  font-size: 10px;
  font-weight: 700;
}

/* Estilo Miniatura */
.thumb-card {
  padding: 8px;
  border-color: #e5e7eb;
}

.mini-sheet {
  width: 100%;
  height: 100%;
  border: 1px solid #f3f4f6;
  display: flex;
}

.mini-col {
  flex: 1;
  border-right: 1px solid #f3f4f6;
}

.mini-col.active {
  background: #fffaf7;
  border: 0.5px solid #fb923c;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2px;
  padding-top: 4px;
}

.mini-dot {
  width: 60%;
  height: 2px;
  background: #fb923c;
  border-radius: 1px;
}

.manager-scroll::-webkit-scrollbar {
  height: 4px;
}

.manager-scroll::-webkit-scrollbar-thumb {
  background: #d1d5db;
  border-radius: 10px;
}
</style>