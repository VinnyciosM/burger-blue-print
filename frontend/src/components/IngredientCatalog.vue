<script setup>
defineProps({
  ingredients: {
    type: Array,
    required: true
  }
})

defineEmits(['close'])
</script>

<template>
  <div class="overlay" @click.self="$emit('close')">
    <div class="catalog-card">
      <div class="card-header">
        <h2 class="title">BIBLIOTECA DE INGREDIENTES</h2>
        <button class="btn-close" @click="$emit('close')">&times;</button>
      </div>
      <div class="catalog-grid">
        <div v-for="item in ingredients" :key="item.id" class="ingredient-item">
          <img :src="item.image" :alt="item.name" class="item-image" />
          <p class="item-name">{{ item.name }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.overlay { 
  position: fixed; 
  inset: 0; 
  background: rgba(17, 24, 39, 0.95); 
  display: flex; 
  justify-content: center; 
  align-items: center; 
  z-index: 10000; 
  padding: 20px; 
}
.catalog-card { 
  background: #fff; 
  width: 100%; 
  max-width: 800px;
  height: 90vh;
  border-radius: 16px; 
  padding: 30px; 
  display: flex; 
  flex-direction: column; 
  gap: 24px; 
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7); 
}
.card-header { 
  display: flex; 
  justify-content: space-between; 
  border-bottom: 2px dashed #f3f4f6; 
  padding-bottom: 15px; 
  flex-shrink: 0;
}
.title { 
  font-size: 13px; 
  font-weight: 900; 
  letter-spacing: 1px; 
  margin: 0; 
}
.btn-close { 
  background: none; 
  border: none; 
  font-size: 24px; 
  color: #9ca3af; 
  cursor: pointer; 
}

.catalog-grid {
  flex: 1;
  overflow-y: auto;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
  gap: 15px;
  padding: 5px;
}

.ingredient-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  background: #f9fafb;
  border-radius: 8px;
  padding: 10px;
  height: 150px;
  width: 150px;
}

.item-image { width: 70px; height: 70px; object-fit: contain; mix-blend-mode: multiply; }
.item-name { margin-top: 8px; font-size: 10px; font-weight: 800; color: #374151; text-transform: uppercase; }

.catalog-grid::-webkit-scrollbar { width: 5px; }
.catalog-grid::-webkit-scrollbar-thumb { background: #d1d5db; border-radius: 10px; }
</style>