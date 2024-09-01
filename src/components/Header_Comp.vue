<template>
  <nav>
    <div class="nav_menu">
      <router-link to="/">Главная</router-link> |
      <router-link to="/convert">Конвертация</router-link>
    </div>
    <div style="float: right;">
      <select v-model="selectedCurrency" @change="updateCurrency">
        <option v-for="currency in currencies" :key="currency" :value="currency">
          {{ currency }}
        </option>
      </select>
    </div>
  </nav>
</template>

<script>
export default {
  props: {
    baseCurrency: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      currencies: ['USD', 'EUR', 'RUB'],
      selectedCurrency: this.baseCurrency,
    };
  },
  methods: {
    updateCurrency() {
      this.$emit('update-base-currency', this.selectedCurrency);
    }
  }
};
</script>
<style scoped>
nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background-color: #f8f8f8;
}

.nav_menu {
  display: flex;
  gap: 20px;
  font-size: 18px;
  
}
a {
  text-decoration: none;
  color: black;
}

::v-deep .router-link-active {
  color: green;
}

.currency-selector select {
  padding: 5px;
  font-size: 16px;
}
</style>
