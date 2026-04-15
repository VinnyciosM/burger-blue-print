<script setup>
defineProps({
  itemName: {
    type: String,
    default: 'este item'
  }
})

defineEmits(['confirm', 'cancel'])
</script>

<template>
  <div class="confirm-overlay" @click.self="$emit('cancel')">
    <div class="confirm-card">
      <h3 class="confirm-title">Tem certeza?</h3>
      <p class="confirm-message">
        Você está prestes a deletar <strong>{{ itemName }}</strong>.<br/>
        Este item será apagado <strong>permanentemente</strong> do banco de dados e você precisará adicioná-lo novamente caso queira usá-lo no futuro.
      </p>
      <div class="confirm-actions">
        <button class="btn-cancel" @click="$emit('cancel')">Cancelar</button>
        <button class="btn-confirm" @click="$emit('confirm')">Sim, deletar</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.confirm-overlay {
  position: fixed;
  inset: 0;
  background: rgba(17, 24, 39, 0.95);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 20000;
  padding: 20px;
}
.confirm-card {
  background: #fff;
  width: 100%;
  max-width: 400px;
  border-radius: 12px;
  padding: 24px;
  display: flex;
  flex-direction: column;
  gap: 16px;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7);
  text-align: center;
}
.confirm-title {
  margin: 0;
  font-size: 18px;
  font-weight: 800;
  color: #111827;
}
.confirm-message {
  font-size: 14px;
  color: #4b5563;
  line-height: 1.5;
  margin: 0;
}
.confirm-actions {
  display: flex;
  justify-content: space-between;
  gap: 12px;
  margin-top: 8px;
}
.btn-cancel {
  flex: 1;
  padding: 10px;
  background: #f3f4f6;
  color: #374151;
  border: none;
  border-radius: 6px;
  font-weight: bold;
  cursor: pointer;
  transition: background 0.2s;
}
.btn-cancel:hover {
  background: #e5e7eb;
}
.btn-confirm {
  flex: 1;
  padding: 10px;
  background: #ef4444;
  color: white;
  border: none;
  border-radius: 6px;
  font-weight: bold;
  cursor: pointer;
  transition: background 0.2s;
}
.btn-confirm:hover {
  background: #dc2626;
}
</style>
