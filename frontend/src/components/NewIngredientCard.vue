<script setup>
import { ref, computed } from 'vue'
// Importação direta do seu banco de dados local
import ingredientsData from '../ingredients.json' 

const emit = defineEmits(['close', 'save'])

const name = ref('')
const category = ref('')
const imagePreview = ref(null)
const fileInput = ref(null)

/**
 * LÓGICA DE CATEGORIAS DINÂMICAS
 * Mapeia o JSON e extrai apenas os nomes de categorias únicas
 */
const dynamicCategories = computed(() => {
  const allCats = ingredientsData.map(item => item.category)
  return [...new Set(allCats)].sort() // O Set remove duplicados automaticamente
})

const triggerFileInput = () => fileInput.value.click()

const handleFileChange = (event) => {
  const file = event.target.files[0]
  if (file && file.type.startsWith('image/')) {
    imagePreview.value = URL.createObjectURL(file)
  }
}

const handleSave = () => {
  if (!name.value || !category.value || !imagePreview.value) {
    alert('Preencha todos os campos.')
    return
  }
  emit('save', {
    id: `ing-${Date.now()}`,
    name: name.value,
    category: category.value,
    image: imagePreview.value
  })
}
</script>

<template>
  <div class="overlay" @click.self="$emit('close')">
    <div class="ingredient-card">
      
      <div class="card-header">
        <h2 class="title">NOVO INGREDIENTE</h2>
        <button class="btn-close" @click="$emit('close')">&times;</button>
      </div>

      <div class="content-grid">
        <div class="left-col">
          <div class="visualizer">
            <img v-if="imagePreview" :src="imagePreview" class="preview-img" />
            <div v-else class="placeholder">SEM IMAGEM</div>
          </div>
        </div>

        <div class="right-col">
          <div class="control-block">
            <input type="file" ref="fileInput" class="hidden" accept="image/*" @change="handleFileChange" />
            <button class="btn-import" @click="triggerFileInput">IMPORTAR IMAGEM LOCAL</button>
          </div>

          <div class="control-block">
            <label>NOME DO INGREDIENTE</label>
            <input type="text" v-model="name" placeholder="Ex: Bacon Crispy" class="input-text" />
          </div>

          <div class="control-block">
            <label>CATEGORIA</label>
            <select v-model="category" class="input-select">
              <option value="" disabled>Selecione a categoria</option>
              <option v-for="cat in dynamicCategories" :key="cat" :value="cat">
                {{ cat.toUpperCase() }}
              </option>
            </select>
          </div>
        </div>
      </div>

      <div class="card-actions">
        <button class="btn-ghost" @click="$emit('close')">CANCELAR</button>
        <button class="btn-save" @click="handleSave">SALVAR NA BIBLIOTECA</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Estilos mantidos conforme o layout de duas colunas solicitado */
.overlay { position: fixed; inset: 0; background: rgba(17, 24, 39, 0.9); display: flex; justify-content: center; align-items: center; z-index: 10000; padding: 20px; }
.ingredient-card { background: #fff; width: 100%; max-width: 650px; border-radius: 16px; padding: 30px; display: flex; flex-direction: column; gap: 24px; }
.card-header { display: flex; justify-content: space-between; border-bottom: 2px dashed #f3f4f6; padding-bottom: 15px; }
.title { font-size: 13px; font-weight: 900; letter-spacing: 1px; margin: 0; }
.btn-close { background: none; border: none; font-size: 24px; color: #9ca3af; cursor: pointer; }

.content-grid { display: flex; gap: 30px; }
.left-col, .right-col { flex: 1; }

.visualizer { aspect-ratio: 1; background: #f9fafb; border: 2px dashed #d1d5db; border-radius: 8px; display: flex; justify-content: center; align-items: center; overflow: hidden; }
.preview-img { max-width: 90%; max-height: 90%; object-fit: contain; mix-blend-mode: multiply; }
.placeholder { font-size: 10px; font-weight: 800; color: #9ca3af; }

.control-block { display: flex; flex-direction: column; gap: 6px; margin-bottom: 18px; }
label { font-size: 9px; font-weight: 900; color: #6b7280; letter-spacing: 1px; }
.hidden { display: none; }

.btn-import { padding: 12px; background: #111827; color: #fff; border: none; border-radius: 6px; font-size: 10px; font-weight: 900; cursor: pointer; }
.input-text, .input-select { padding: 14px; border: 1px solid #d1d5db; border-radius: 6px; font-size: 12px; font-weight: 600; outline: none; text-transform: uppercase; }

.card-actions { display: flex; justify-content: flex-end; gap: 12px; border-top: 2px dashed #f3f4f6; padding-top: 20px; }
.card-actions button { padding: 14px 24px; font-weight: 900; font-size: 11px; border-radius: 8px; cursor: pointer; border: none; }
.btn-ghost { background: #f3f4f6; color: #4b5563; }
.btn-save { background: #111827; color: #fff; }
</style>