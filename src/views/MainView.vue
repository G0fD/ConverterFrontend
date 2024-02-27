<script setup>
import {onMounted, ref} from "vue";
import {API_URL} from "@/common/constant";

const currencies = ref([])

onMounted(async ()=>{
  await fetch(API_URL + "/getAll")
})

const response = await fetch(API_URL)

</script>

<template>

</template>

<style scoped>

</style>