<script setup lang="ts">

import {ref} from 'vue'
const newAmount = ref('')
const newDescription = ref('')

const transactions = ref([])

function addTransaction(){
  const amount = Number(newAmount.value)
  const description = newDescription.value.trim()

  transactions.value.push({
    id: Date.now(),
    amount: amount,
    description: description
  })
  newAmount.value = '';
  newDescription.value = '';
}


</script>

<template>
  <div>
      <h1>Transações</h1>
      <button>Nova transação</button>
  </div>
  <form @submit.prevent="addTransaction">
      <label for="amount">Valor:</label>
      <input type="number" id="amount" step="0.01" placeholder="R$ 0,00" v-model="newAmount">
      <label for="description">Descrição:</label>
      <input type="text" id="description" placeholder="Informe a descrição da transação aqui" v-model="newDescription"> 
      <button type="submit">Adicionar</button>
  </form>
  <div id="list">
    <ul>
      <li v-for="transaction in transactions" :key="transaction.id">
        <span>{{transaction.amount}}</span>
        <span>{{transaction.description}}</span>
      </li>
    </ul>

  </div>
</template>
