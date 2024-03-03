<script setup>
import {onMounted, ref} from "vue";
import {API_URL} from "@/common/constant";

const currencies = ref([])
const active = ref([])

onMounted(async () =>{
  await updateDatabase()
  await getCurrencies()
})

async function updateDatabase(){
  try{
    const response = await fetch(API_URL + "/updateDatabase", {
      method: "GET",
      headers: {
        'Content-Type': 'application/json'
      }
    })
    const message = await response.json()
    window.alert(message.message)
  }catch (error){
    console.error('Error updating database, check connection. ' + error)
    window.alert('Error updating database, check connection. ' + error)
  }
}

async function getCurrencies(){
  try{
    const response = await fetch(API_URL + "/getAll", {
      method: "GET",
      headers: {
        'Content-Type': 'application/json'
      }
    })
    currencies.value = await response.json()
  } catch (error){
    console.error('Error while fetching the data, check connection. ' + error)
    window.alert('Error while fetching the data, check connection. ' + error)
  }
}

async function recalculate(code, value){
  try{
    const response = await fetch(API_URL + "/recalculate", {
      method: "POST",
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        code: code,
        value: value
      })
    })

    const data = await response.json();
    if(data.message) {
      window.alert(data.message)
    } else {
      currencies.value = data
      active.value = active.value.map(item => {
        const foundCurrency = currencies.value.find(currency => currency.code === item.code);
        return foundCurrency ? foundCurrency : item;
      });
    }
  } catch (error){
    console.error('Error while recalculating, check connection. ' + error)
    window.alert('Error while recalculating, check connection. ' + error)
  }
}

</script>

<template>
  <header>
    <h1>Currency converter</h1>
  </header>
  <main>
    <section v-show="currencies.length > 0">
      <div class="element" v-for="currency in active" :key="currency.code">
        <p>{{currency.code}}</p>
        <input type="number" :value="currency.value" :id="currency.code"
               @change = "recalculate(currency.code, $event.target.value)"/>
        <p>{{currency.name}}</p>
      </div>
    </section>
    <aside>
      <label class="currency-list" v-for="currency in currencies" :key="currency.code">
        <input type="checkbox" :id="currency.code" :value="currency"
               v-model="active" >
        {{currency.code}}
      </label>
    </aside>
  </main>
  <footer>

  </footer>
</template>

<style scoped>

</style>