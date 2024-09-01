<template>
  <div style="text-align: center;">
    <h1>Конвертация валют</h1>
    <div class="converter-row">
      <select v-model="currencyFrom">
        <option v-for="currency in currencies" :key="currency" :value="currency">
          {{ currency }}
        </option>
      </select>
      <input 
        type="number" 
        v-model.number="amountFrom" 
        @input="convert('from')" 
        @blur="validateInput('from')"
      />
      <span v-if="errors.from" class="error">{{ errors.from }}</span>
    </div>
    <div class="converter-row">
      <select v-model="currencyTo">
        <option v-for="currency in currencies" :key="currency" :value="currency">
          {{ currency }}
        </option>
      </select>
      <input 
        type="number" 
        v-model.number="amountTo" 
        @input="convert('to')" 
        @blur="validateInput('to')"
      />
      <span v-if="errors.to" class="error">{{ errors.to }}</span>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      currencies: ['USD', 'EUR', 'RUB'],
      currencyFrom: 'RUB',
      currencyTo: 'USD',
      amountFrom: 0,
      amountTo: 0,
      rates: {},
      errors: {
        from: '',
        to: ''
      }
    };
  },
  created() {
    this.fetchRates();
  },
  watch: {
    currencyFrom() {
      this.convert('from');
    },
    currencyTo() {
      this.convert('from');
    }
  },
  methods: {
    async fetchRates() {
  try {
    const response = await axios.get('https://status.neuralgeneration.com/api/currency');
    console.log('API Response:', response.data);

    const rawRates = response.data;
    
    this.rates = {
      "USD": rawRates["usd-rub"],  
      "EUR": rawRates["eur-rub"],
      "RUB": 1 
    };
    
    this.convert('from');  
  } catch (error) {
    console.error("Ошибка получения курсов валют:", error);
  }
},

convert(direction) {
  if (!this.rates || !this.rates[this.currencyFrom] || !this.rates[this.currencyTo]) {
    console.error("Некорректные данные для конвертации", this.rates);
    return;
  }

  if (direction === 'from') {
    if (this.currencyFrom === 'RUB') {
      this.amountTo = (this.amountFrom / this.rates[this.currencyTo]).toFixed(2);
    } else if (this.currencyTo === 'RUB') {
      this.amountTo = (this.amountFrom * this.rates[this.currencyFrom]).toFixed(2);
    } else {
      this.amountTo = (this.amountFrom * this.rates[this.currencyFrom] / this.rates[this.currencyTo]).toFixed(2);
    }
  } 
  else {
      if (this.currencyTo === 'RUB') {
        this.amountFrom = (this.amountTo / this.rates[this.currencyFrom]).toFixed(2);
      } else if (this.currencyFrom === 'RUB') {
        this.amountFrom = (this.amountTo * this.rates[this.currencyTo]).toFixed(2);
      } else {
        this.amountFrom = (this.amountTo * this.rates[this.currencyTo] / this.rates[this.currencyFrom]).toFixed(2);
      }
    }
  },
    validateInput(field) {
      if (isNaN(this[field === 'from' ? 'amountFrom' : 'amountTo'])) {
        this.errors[field] = "Введите числовое значение.";
      } else {
        this.errors[field] = '';
      }
    }
  }
};
</script>

<style>
.converter-row {
  margin-bottom: 10px;
}

.error {
  color: red;
  font-size: 0.9em;
}
</style>
