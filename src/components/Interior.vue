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
import {COIN_TYPES, PRODUCT_TYPES, PRODUCT_PRICE} from '../constants/config.js'

export default {
  name: 'Interior',
  data() {
    return {
      COIN_TYPES: COIN_TYPES,
      PRODUCT_TYPES: PRODUCT_TYPES,
      PRODUCT_PRICE: PRODUCT_PRICE,
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
      },
      stockProducts:
      {
        "A": 2,
        "B": 2
      },
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
    getCanBuyNow() {
      // 購入可能条件
      // 在庫有 AND 投入金額 >= 商品金額 AND 投入済み硬貨＋硬貨在庫から釣銭を払い出せる
      let canBuyNow = {};
      let inputAmount = this.getInputAmount();
      for(let p in PRODUCT_TYPES) {
        canBuyNow[PRODUCT_TYPES[p]] = (this.stockProducts[PRODUCT_TYPES[p]] > 0) && 
                          (inputAmount >= PRODUCT_PRICE[PRODUCT_TYPES[p]]) && 
                          (this.hasChangeFor(inputAmount - PRODUCT_PRICE[PRODUCT_TYPES[p]]));
      }
      return canBuyNow;
    },
    getOutOfStock() {
      let outOfStock = {};
      for(let p in PRODUCT_TYPES) {
        outOfStock[PRODUCT_TYPES[p]] = this.stockProducts[PRODUCT_TYPES[p]] < 1;
      }
      return outOfStock;
    },
    isEnoughChange() {
      let coins = this.stockCoins;
      
      // 100円以上の硬貨で400円が作れ、
      // 10円以上の硬貨で90円が作れれば、釣り銭充足と判定
      // 100円以上のお釣りを50/10円硬貨で返却させない
      let isEnoughHundreds = coins["100"] >= 4;
      let isEnoughTens = coins["10"] >= 9 || (coins["50"] >= 1 && coins["10"] >= 4);
      
      return isEnoughHundreds && isEnoughTens;
    },
    hasChangeFor(changeAmount) {
      for(let c in COIN_TYPES) {
        let availableCoins = this.inputCoins[COIN_TYPES[c]] + this.stockCoins[COIN_TYPES[c]];
        let coin = parseInt(COIN_TYPES[c], 10);
        if(changeAmount >= coin && availableCoins > 0) {
          changeAmount -= coin * Math.min(availableCoins, Math.floor(changeAmount / coin));
        }
        if(changeAmount === 0) {
          return true;
        }
      }
      return false;
    },
    canAcceptCoin(coin) {
      return COIN_TYPES.includes(coin);
    },
    addInputCoin(coin) {
      this.inputCoins[coin] += 1;
    },
    returnInputCoin() {
      // inputCoinsから、一番大きな硬貨を返却します。
      for(let c in COIN_TYPES) {
        if(this.inputCoins[COIN_TYPES[c]] > 0) {
          this.inputCoins[COIN_TYPES[c]] -= 1;
          return COIN_TYPES[c];
        }
      }
      return "";
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
