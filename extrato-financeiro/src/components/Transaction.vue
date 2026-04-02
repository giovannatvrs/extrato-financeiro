<script setup lang="ts">

defineProps({
    id: Number,
    amount: Number,
    description: String
})

</script>

<template>
        <li>
            <span>{{amount}}</span>
            <span>{{description}}</span>
            <span><i class="fa-solid fa-angle-up"></i></span>
            <span><i class="fa-solid fa-angle-down"></i></span>        
            <span @click="$emit('deleteTransaction', id)"><i class="fa-regular fa-trash-can"></i></span>
        </li>
</template>