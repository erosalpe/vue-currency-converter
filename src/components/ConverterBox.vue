<script setup>
    import {ref, onMounted, watch} from 'vue'
    import axios from 'axios'
    
    const firstCurrencyValue = ref(0);
    const secondCurrencyValue = ref(0);
    const firstCurrencyName = ref('');
    const secondCurrencyName = ref('');
    const currencyList = ref([]);

    const initialize = () => {
        axios.get('https://api.frankfurter.app/currencies')
        .then(response => {
            console.log(response.data);
            Object.entries(response.data).forEach(([currency, value]) => {
            currencyList.value.push({
                valuta: currency,
                nome: value,
            });
        });
        })
        .catch(error => {
            console.error(error);
        });
    };

    onMounted(() => initialize());

    const converti = () => {
        if(firstCurrencyValue && (firstCurrencyName && secondCurrencyName)){
            axios.get(`https://api.frankfurter.app/latest?base=${firstCurrencyName.value}&symbols=${secondCurrencyName.value}`)
            .then(response =>{
                console.log(response.data);
                const exchangeRate = Object.values(response.data.rates);
                secondCurrencyValue.value = firstCurrencyValue.value * exchangeRate;
            })
            .catch(error => {
                consol.error("Errore durante il recupero dei tassi di cambio:", error);
            })
        }
    };
    
</script>






<template>
    <div class="container mt-5">
        <div id="converter-container">
            <h1>Currency Converter</h1>
            <div>
                <div class="d-flex justify-content-between align-items-center">
                    <input type="number" name="currency-first" id="currency-first" v-model="firstCurrencyValue">
                    <select name="currencyFirst" id="currencyFirst" v-model="firstCurrencyName">
                        <option v-for="element in currencyList" :value="element.valuta">{{ element.nome }}</option>
                    </select>
                    <span>=></span>
                    <select name="currencySecond" id="currencySecond" v-model="secondCurrencyName">
                        <option v-for="element in currencyList" :value="element.valuta">{{ element.nome }}</option>
                    </select>
                    <button @click="converti()" class="btn btn-primary">Converti</button>
                </div>
            </div>
            <h3 v-if="secondCurrencyValue != 0">{{ secondCurrencyValue }}</h3> 
        </div>
    </div>
</template>








<style lang="scss">
    #converter-container{
        border: 3px solid grey;
        border-radius: 20px;
        padding: 10px;
    }
    h1{
        color: $color-primary;
    }
    /* Chrome, Safari, Edge, Opera */
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
    }

    /* Firefox */
    input[type=number] {
    -moz-appearance: textfield;
    }
</style>