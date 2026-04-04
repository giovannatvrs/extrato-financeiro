<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps<{
  id: number
  amount: number
  description: string
  runningBalance: number
}>()

const emit = defineEmits<{
  (e: 'deleteTransaction', id: number): void
  (e: 'moveUp', id: number): void
  (e: 'moveDown', id: number): void
}>()

const formattedAmount = computed(() => {
  const prefix = props.amount >= 0 ? 'R$ ' : 'R$ -'
  const absVal = Math.abs(props.amount)
  return `${props.amount >= 0 ? 'R$' : 'R$ -'}${absVal.toLocaleString('pt-BR', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`
})

const formattedBalance = computed(() => {
  const abs = Math.abs(props.runningBalance)
  const formatted = abs.toLocaleString('pt-BR', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
  return props.runningBalance < 0 ? `R$ -${formatted}` : `R$ ${formatted}`
})

const isPositive = computed(() => props.amount >= 0)
const balancePositive = computed(() => props.runningBalance >= 0)
</script>

<template>
  <div class="transaction-row">
    <div class="col col-amount" :class="isPositive ? 'positive' : 'negative'">
      {{ formattedAmount }}
    </div>
    <div class="col col-description">{{ description }}</div>
    <div class="col col-balance">
      <span class="balance-badge" :class="balancePositive ? 'badge-green' : 'badge-red'">
        {{ formattedBalance }}
      </span>
    </div>
    <div class="col col-actions">
      <button class="action-btn" @click="$emit('moveUp', id)" title="Mover para cima">
        <i class="fa-solid fa-angle-up"></i>
      </button>
      <button class="action-btn" @click="$emit('moveDown', id)" title="Mover para baixo">
        <i class="fa-solid fa-angle-down"></i>
      </button>
      <button class="action-btn action-btn--delete" @click="$emit('deleteTransaction', id)" title="Excluir">
        <i class="fa-regular fa-trash-can"></i>
      </button>
    </div>
  </div>
</template>

<style scoped>
.transaction-row {
  display: grid;
  grid-template-columns: 140px 1fr 160px 120px;
  align-items: center;
  padding: 0 24px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  background: #fff;
  margin: 6px 16px;
  transition: background 0.15s;
  flex: 1;
  min-height: 72px;
}
.transaction-row:hover {
  background: #fafafa;
}

.col {
  font-size: 14px;
}

.col-amount {
  font-weight: 600;
  font-size: 14px;
}
.col-amount.positive {
  color: #2ecc71;
}
.col-amount.negative {
  color: #e74c3c;
}

.col-description {
  color: #333;
  font-size: 14px;
}

.col-balance {
  display: flex;
  justify-content: center;
}

.balance-badge {
  display: inline-block;
  padding: 5px 14px;
  border-radius: 4px;
  font-weight: 700;
  font-size: 13px;
  color: #fff;
  white-space: nowrap;
}
.badge-green {
  background: #27ae60;
}
.badge-red {
  background: #e74c3c;
  color: #ffe600;
}

.col-actions {
  display: flex;
  align-items: center;
  gap: 6px;
  justify-content: center;
}

.action-btn {
  background: none;
  border: none;
  cursor: pointer;
  color: #555;
  font-size: 15px;
  padding: 5px 7px;
  border-radius: 4px;
  transition: color 0.15s, background 0.15s;
  display: flex;
  align-items: center;
  justify-content: center;
}
.action-btn:hover {
  color: #222;
  background: #f0f0f0;
}
.action-btn--delete:hover {
  color: #e74c3c;
  background: #ffeaea;
}
</style>