<template>
  <div id="app">
    <Header_Comp :baseCurrency="baseCurrency" @update-base-currency="setBaseCurrency" />
    <router-view :baseCurrency="baseCurrency" />
    <div v-if="showCurrencyModal" class="currency-modal">
      <div class="currency-modal-content">
        <h2>Выберите основную валюту</h2>
        <select v-model="selectedCurrency">
          <option v-for="currency in currencies" :key="currency" :value="currency">
            {{ currency }}
          </option>
        </select>
        <button @click="saveCurrency">Сохранить</button>
      </div>
    </div>
  </div>
</template>

<script>
import Header_Comp from './components/Header_Comp.vue';

export default {
  data() {
    return {
      baseCurrency: localStorage.getItem('baseCurrency') || null,
      selectedCurrency: 'RUB',
      showCurrencyModal: localStorage.getItem('baseCurrency') ? false : true,
      currencies: ['USD', 'EUR', 'RUB'],
    };
  },
  methods: {
    setBaseCurrency(currency) {
      this.baseCurrency = currency;
      localStorage.setItem('baseCurrency', currency);
    },
    saveCurrency() {
      this.setBaseCurrency(this.selectedCurrency);
      this.showCurrencyModal = false;
    }
  },
  components: {
    Header_Comp
  }
};
</script>

<style>
.currency-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.currency-modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 5px;
  text-align: center;
}
</style>
