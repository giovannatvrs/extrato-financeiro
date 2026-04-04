<script setup lang="ts">
import { ref } from 'vue'

const emit = defineEmits<{
  (e: 'submit-form', payload: { id: number; amount: number; description: string }): void
  (e: 'close'): void
}>()

const newAmount = ref('')
const newDescription = ref('')

const handleSubmit = () => {
  if (!newAmount.value || !newDescription.value.trim()) return
  emit('submit-form', {
    id: Date.now(),
    amount: Number(newAmount.value),
    description: newDescription.value.trim()
  })
  newAmount.value = ''
  newDescription.value = ''
}
</script>

<template>
  <div class="modal-backdrop" @click.self="$emit('close')">
    <div class="modal">
      <div class="modal-header">
        <h2>Nova Transação</h2>
        <button class="modal-close" @click="$emit('close')">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>
      <form class="modal-body" @submit.prevent="handleSubmit">
        <div class="form-group">
          <label for="description">Descrição</label>
          <input
            id="description"
            type="text"
            placeholder="Informe a descrição da transação"
            v-model="newDescription"
            required
          />
        </div>
        <div class="form-group">
          <label for="amount">Valor</label>
          <input
            id="amount"
            type="number"
            step="0.01"
            placeholder="Ex: 150.00 (negativo para despesa)"
            v-model="newAmount"
            required
          />
        </div>
        <div class="form-footer">
          <button type="button" class="btn-cancel" @click="$emit('close')">Cancelar</button>
          <button type="submit" class="btn-submit">Adicionar</button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
.modal-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  animation: fadeIn 0.15s ease;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

.modal {
  background: #fff;
  border-radius: 10px;
  width: 480px;
  max-width: 95vw;
  box-shadow: 0 8px 32px rgba(0,0,0,0.18);
  animation: slideUp 0.2s ease;
}

@keyframes slideUp {
  from { transform: translateY(20px); opacity: 0; }
  to   { transform: translateY(0);    opacity: 1; }
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px 24px 16px;
  border-bottom: 1px solid #eee;
}

.modal-header h2 {
  font-size: 18px;
  font-weight: 700;
  color: #1a1a1a;
}

.modal-close {
  background: none;
  border: none;
  font-size: 18px;
  color: #888;
  cursor: pointer;
  padding: 4px 8px;
  border-radius: 4px;
  transition: color 0.15s, background 0.15s;
}
.modal-close:hover {
  color: #222;
  background: #f0f0f0;
}

.modal-body {
  padding: 20px 24px 24px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 6px;
  margin-bottom: 16px;
}

.form-group label {
  font-size: 13px;
  font-weight: 600;
  color: #555;
}

.form-group input {
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.15s;
}
.form-group input:focus {
  border-color: #1a1a1a;
}

.form-footer {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  margin-top: 8px;
}

.btn-cancel {
  padding: 9px 20px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: #fff;
  font-size: 14px;
  cursor: pointer;
  transition: background 0.15s;
}
.btn-cancel:hover {
  background: #f5f5f5;
}

.btn-submit {
  padding: 9px 20px;
  border: none;
  border-radius: 6px;
  background: #1a1a1a;
  color: #fff;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s;
}
.btn-submit:hover {
  background: #333;
}
</style>