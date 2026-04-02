<script setup lang="ts">

import {ref} from 'vue'
import Transaction from './components/Transaction.vue'
import TransactionForm from './components/TransactionForm.vue'

interface Transaction{
  id: number,
  amount: number,
  description: string
}

const newAmount = ref(0)
const newDescription = ref('')

const transactions = ref<Transaction[]>([
  {
    id: 1, amount: 36.99,
    description: 'Pix recebido'
  },
  {
    id: 2, amount: 2568.50,
    description: 'Salário'
  },
  {
    id: 3, amount: 158.75,
    description: 'Compras na farmácia'
  }
])

const addTransaction = (newTransaction: { id: number; amount: number; description: string }) => {
  transactions.value.push(newTransaction)
};

function deleteItem(id: number){
  transactions.value = transactions.value.filter((t) => t.id !== id)
}

</script>

<template>
 
  <div>
      <h1>Transações</h1>
      <button>Nova transação</button>
  </div>
  <TransactionForm @submit-form="addTransaction"></TransactionForm>
  <ul>
      <Transaction v-for="(item, index) in transactions"
        :id="item.id"
        :amount="item.amount"
        :description="item.description"
        v-on:deleteTransaction="deleteItem">
      </Transaction>
  </ul>
   
</template>
