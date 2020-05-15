<template>
  <div class="interior">
    <h1>内観</h1>
    <hr>
    <h3>投入済硬貨</h3>
    <table class="table">
      <thead>
        <tr>
          <th>硬貨</th>
          <th>枚数</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="coin in COIN_TYPES" v-bind:key="coin">
          <td>{{ coin }}</td>
          <td>{{ inputCoinsInitialized ? inputCoins[coin] : "--"}}</td>
        </tr>
      </tbody>
    </table>
    <h3>硬貨在庫</h3>
    <table class="table">
      <thead>
        <tr>
          <th>硬貨</th>
          <th>枚数</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="coin in COIN_TYPES" v-bind:key="coin">
          <td>{{ coin }}</td>
          <td>{{ stockCoinsInitialized ? stockCoins[coin] : "--"}}</td>
        </tr>
      </tbody>
    </table>
    <h3>商品在庫</h3>
    <table class="table">
      <thead>
        <tr>
          <th>商品名</th>
          <th>在庫数</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in PRODUCT_TYPES" v-bind:key="product">
          <td>{{ product }}</td>
          <td>{{ productsInitialized ? stockProducts[product] : "--"}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import {COIN_TYPES, PRODUCT_TYPES} from '../constants/config.js'

export default {
  name: 'Interior',
  props: {
    stockProducts: Array
  },
  data() {
    return {
      COIN_TYPES: COIN_TYPES,
      PRODUCT_TYPES: PRODUCT_TYPES,
      inputCoinsInitialized: false,
      stockCoinsInitialized: false,
      productsInitialized: false,
      inputCoins:
      {
        "500": 0,
        "100": 0,
        "50": 0,
        "10": 0
      },
      stockCoins:
      {
        "500": 0,
        "100": 9,
        "50": 9,
        "10": 9
      }
    };
  },
  methods : {
    getAmount(coins) {
      let amount = 0;
      for (let i in COIN_TYPES) {
        amount += parseInt(COIN_TYPES[i], 10) * coins[COIN_TYPES[i]];
      }
      return amount;
    },
    initializeInputCoins() {
      this.inputCoinsInitialized = true;
    },
    initializeStockCoins() {
      this.stockCoinsInitialized = true;
    },
    initializeProducts() {
      this.productsInitialized = true;
    },
    getInputAmount() {
      return this.getAmount(this.inputCoins);
    },
    isEnoughChange() {
      let coins = this.stockCoins;
      
      // 100円以上の硬貨で400円が作れ、
      // 10円以上の硬貨で90円が作れれば、釣り銭充足と判定
      // 100円以上のお釣りを50/10円硬貨で返却させない
      let isEnoughHundreds = coins["100"] >= 4;
      let isEnoughTens = coins["10"] >= 9 || (coins["50"] >= 1 && coins["10"] >= 4);
      
      return isEnoughHundreds && isEnoughTens;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 20px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.table {
  border: 1px solid #eee;
  border-collapse: collapse;
}

.table th,
.table td {
  border: 1px solid #dedede;
  padding: .5em;
  text-align: center;
}
</style>
