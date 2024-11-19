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

    watch([firstCurrencyName, secondCurrencyName, firstCurrencyValue], ([newFirst, newLast, newValue], [oldFirst, oldLast, oldValue])=> {
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
    });
    
</script>






<template>
    <div class="container mt-5">
        <div id="converter-container">
            <h1 class="text-center">Currency Converter</h1>
            <div class="d-flex justify-content-center align-items-center py-2">
                <div class="d-flex flex-column gap-4">
                    <input type="number" name="currency-first" id="currency-first" class="w-50 m-0 m-auto" v-model="firstCurrencyValue">
                    <div class="d-flex align-items-center">
                        <select name="currencyFirst" id="currencyFirst" v-model="firstCurrencyName">
                            <option v-for="element in currencyList" :value="element.valuta">{{ element.nome }}</option>
                        </select>
        
                        <span id="arrow"> &#11020; </span>
                        
                        <select name="currencySecond" id="currencySecond" v-model="secondCurrencyName">
                            <option v-for="element in currencyList" :value="element.valuta">{{ element.nome }}</option>
                        </select>
                    </div>
                </div>
            </div>
            <h3 class="text-center" v-if="secondCurrencyValue != 0">{{firstCurrencyValue}} {{ firstCurrencyName }} = {{ secondCurrencyValue }} {{ secondCurrencyName }}</h3> 
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

    #arrow{
        font-size: 35px;
        padding-bottom:5px;
        margin-inline: 5px;
    }
</style>