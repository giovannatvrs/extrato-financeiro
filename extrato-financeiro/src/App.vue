<script setup lang="ts">
import { ref, computed } from 'vue'
import Transaction from './components/Transaction.vue'
import TransactionForm from './components/TransactionForm.vue'

interface TransactionItem {
  id: number
  amount: number
  description: string
}

const PAGE_SIZE = 5

const transactions = ref<TransactionItem[]>([
  { id: 1, amount: 26.78,    description: 'Pix recebido' },
  { id: 2, amount: 2556.23,  description: 'Salário' },
  { id: 3, amount: -57.43,   description: 'Compras  na farmácia' },
  { id: 4, amount: -1800.00, description: 'Fatura do cartão de crédito' },
  { id: 5, amount: -950.25,  description: 'Aluguel' },
  { id: 6, amount: 500.00,   description: 'Freelance' },
  { id: 7, amount: -120.00,  description: 'Internet' },
])

const showForm = ref(false)
const currentPage = ref(1)

// Running balance for each transaction (cumulative)
const withBalance = computed(() => {
  let running = 0
  return transactions.value.map(t => {
    running += t.amount
    return { ...t, runningBalance: running }
  })
})

const totalPages = computed(() => Math.max(1, Math.ceil(transactions.value.length / PAGE_SIZE)))

const paginated = computed(() => {
  const start = (currentPage.value - 1) * PAGE_SIZE
  return withBalance.value.slice(start, start + PAGE_SIZE)
})

const visiblePages = computed(() => {
  const total = totalPages.value
  if (total <= 5) return Array.from({ length: total }, (_, i) => i + 1)
  const pages: (number | '...')[] = []
  pages.push(1)
  if (currentPage.value > 3) pages.push('...')
  for (let i = Math.max(2, currentPage.value - 1); i <= Math.min(total - 1, currentPage.value + 1); i++) {
    pages.push(i)
  }
  if (currentPage.value < total - 2) pages.push('...')
  pages.push(total)
  return pages
})

function addTransaction(t: TransactionItem) {
  transactions.value.push(t)
  showForm.value = false

  currentPage.value = totalPages.value
}

function deleteTransaction(id: number) {
  transactions.value = transactions.value.filter(t => t.id !== id)
  if (currentPage.value > totalPages.value) currentPage.value = totalPages.value
}

function moveUpTransaction(id: number) {
  const idx = transactions.value.findIndex(t => t.id === id)
  if (idx <= 0) return
  const arr = [...transactions.value]
  ;[arr[idx - 1], arr[idx]] = [arr[idx], arr[idx - 1]]
  transactions.value = arr
}

function moveDownTransaction(id: number) {
  const idx = transactions.value.findIndex(t => t.id === id)
  if (idx === -1 || idx >= transactions.value.length - 1) return
  const arr = [...transactions.value]
  ;[arr[idx], arr[idx + 1]] = [arr[idx + 1], arr[idx]]
  transactions.value = arr
}

function goToPage(page: number | '...') {
  if (page === '...') return
  currentPage.value = page
}
</script>

<template>
  <div class="page-wrapper">
    <div class="card">
      <!-- Header -->
      <div class="card-header">
        <h1 class="page-title">Transações</h1>
        <button class="btn-new" @click="showForm = true">
          <i class="fa-solid fa-plus"></i> Nova Transação
        </button>
      </div>

      <!-- Table header -->
      <div class="table-header">
        <div class="th th-amount">Valor</div>
        <div class="th th-description">Descrição</div>
        <div class="th th-balance">Saldo Total</div>
        <div class="th th-actions">Ações</div>
      </div>

      <!-- Rows -->
      <div class="table-body">
        <Transaction
          v-for="item in paginated"
          :key="item.id"
          :id="item.id"
          :amount="item.amount"
          :description="item.description"
          :runningBalance="item.runningBalance"
          @delete-transaction="deleteTransaction"
          @move-up="moveUpTransaction"
          @move-down="moveDownTransaction"
        />
        <!-- Linhas vazias para completar sempre 5 slots -->
        <div
          v-for="n in (PAGE_SIZE - paginated.length)"
          :key="'empty-' + n"
          class="empty-row"
        ></div>
        <div v-if="transactions.length === 0" class="empty-state">
          Nenhuma transação encontrada.
        </div>
      </div>

      <!-- Pagination -->
      <div class="pagination" v-if="totalPages > 1">
        <button class="page-btn" :disabled="currentPage === 1" @click="currentPage--">
          <i class="fa-solid fa-angle-left"></i>
        </button>
        <template v-for="p in visiblePages" :key="p">
          <button
            v-if="p !== '...'"
            class="page-btn"
            :class="{ active: currentPage === p }"
            @click="goToPage(p)"
          >{{ p }}</button>
          <span v-else class="page-ellipsis">…</span>
        </template>
        <button class="page-btn" :disabled="currentPage === totalPages" @click="currentPage++">
          <i class="fa-solid fa-angle-right"></i>
        </button>
      </div>
    </div>
  </div>

  <!-- Modal -->
  <TransactionForm
    v-if="showForm"
    @submit-form="addTransaction"
    @close="showForm = false"
  />
</template>

<style scoped>
.page-wrapper {
  width: 100%;
  max-width: calc(100vw - 48px);
}

.card {
  background: #fff;
  border-radius: 12px;
  border: 1px solid #e0e0e0;
  box-shadow: 0 2px 16px rgba(0,0,0,0.08);
  overflow: hidden;
  min-height: calc(100vh - 64px);
  display: flex;
  flex-direction: column;
}

/* Header */
.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 28px 28px 20px;
}

.page-title {
  font-size: 24px;
  font-weight: 800;
  color: #1a1a1a;
  letter-spacing: -0.5px;
}

.btn-new {
  background: #1a1a1a;
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: 10px 18px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 7px;
  transition: background 0.15s, transform 0.1s;
}
.btn-new:hover {
  background: #333;
  transform: translateY(-1px);
}
.btn-new:active {
  transform: translateY(0);
}

/* Table header */
.table-header {
  display: grid;
  grid-template-columns: 180px 1fr 200px 140px;
  padding: 16px 32px;
  border-bottom: 1px solid #eee;
  background: #fafafa;
}

.th {
  font-size: 14px;
  font-weight: 700;
  color: #888;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.th-amount     { }
.th-description{ }
.th-balance    { text-align: center; }
.th-actions    { text-align: center; }

/* Table body */
.table-body {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 8px 0;
}

/* Linha vazia placeholder para manter os 5 slots */
.empty-row {
  min-height: 72px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  background: #fff;
  margin: 6px 16px;
  flex: 1;
}

.empty-state {
  padding: 40px;
  text-align: center;
  color: #aaa;
  font-size: 14px;
}

/* Pagination */
.pagination {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  padding: 20px;
  border-top: 1px solid #eee;
}

.page-btn {
  min-width: 34px;
  height: 34px;
  padding: 0 8px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: #fff;
  color: #333;
  font-size: 13px;
  font-weight: 500;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.15s, border-color 0.15s, color 0.15s;
}
.page-btn:hover:not(:disabled) {
  background: #f5f5f5;
  border-color: #bbb;
}
.page-btn:disabled {
  opacity: 0.35;
  cursor: not-allowed;
}
.page-btn.active {
  background: #1a1a1a;
  color: #fff;
  border-color: #1a1a1a;
}

.page-ellipsis {
  color: #999;
  font-size: 14px;
  padding: 0 4px;
  user-select: none;
}
</style>
