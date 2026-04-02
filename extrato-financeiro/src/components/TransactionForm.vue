<script setup>
    import { ref } from 'vue';
    const emit = defineEmits(['submit-form']);
    const newAmount = ref('');
    const newDescription = ref('');

    const handleSubmit = () =>{
        emit('submit-form', {id: Date.now(), amount: Number(newAmount.value), description: newDescription.value.trim()});
        newAmount.value = '';
        newDescription.value = '';
    };

</script>
<template>
    <form @submit.prevent="handleSubmit">
      <label for="amount">Valor:</label>
      <input type="number" id="amount" step="0.01" placeholder="R$ 0,00" v-model="newAmount">
      <label for="description">Descrição:</label>
      <input type="text" id="description" placeholder="Informe a descrição da transação aqui" v-model="newDescription"> 
      <button type="submit">Adicionar</button>
  </form>
</template>