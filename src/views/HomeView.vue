<template>
  <div style="text-align: center;">
    <h1>Курсы валют по отношению к {{ baseCurrency }}</h1>
    <ul class="currency-list">
      <li v-for="(rate, currency) in displayedRates" :key="currency">
        1 {{ currency }} = {{ rate.toFixed(2) }} {{ baseCurrency }}
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    baseCurrency: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      rates: {},
      displayedRates: {}
    };
  },
  created() {
    this.fetchRates();
  },
  watch: {
    baseCurrency() {
      this.fetchRates();
    }
  },
  methods: {
    async fetchRates() {
      try {
        const response = await axios.get('https://status.neuralgeneration.com/api/currency');
        const rawRates = response.data;

        // Сохраняем курсы валют к RUB
        this.rates = {
          "USD": rawRates["usd-rub"],
          "EUR": rawRates["eur-rub"],
          "RUB": 1
        };

        // Вычисляем отображаемые курсы валют
        this.calculateDisplayedRates();
      } catch (error) {
        console.error("Ошибка получения курсов валют:", error);
      }
    },
    calculateDisplayedRates() {
      if (this.baseCurrency === 'RUB') {
        this.displayedRates = {
          "USD": 1 / this.rates["USD"],
          "EUR": 1 / this.rates["EUR"],
          "RUB": 1
        };
      } else {
        this.displayedRates = {
          "USD": this.rates["USD"] / this.rates[this.baseCurrency],
          "EUR": this.rates["EUR"] / this.rates[this.baseCurrency],
          "RUB": this.rates["RUB"] / this.rates[this.baseCurrency]
        };
      }
    }
  }
};
</script>
<style scoped>
.currency-list {
  list-style: none; 
  padding-left: 0;
}

.currency-list li {
  margin-bottom: 5px;
}
</style>