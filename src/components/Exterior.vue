<template>
  <div class="hello">
    <h1>外観</h1>
    <hr>
    <h3>電源スイッチ</h3>
    {{ power }}
    <h3>商品</h3>
    <table class="table">
      <thead>
        <tr>
          <th>商品名</th>
          <th>値段</th>
          <th>購入可能</th>
          <th>品切れ</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in PRODUCT_TYPES" v-bind:key="product">
          <td>{{ product }}</td>
          <td>{{ PRODUCT_PRICE[product] + "円"}}</td>
          <td>{{ getCanBuyLamp(canBuyNow[product]) }}</td>
          <td>{{ getOutOfStockLamp(outOfStock[product])}}</td>
        </tr>
      </tbody>
    </table>
    <h3>投入金額</h3>
    {{ inputAmountYen }}
    <h3>釣り銭切れランプ</h3>
    100円：{{ outOfChangeLamp("100") }}<br>10&nbsp;円&nbsp;：{{ outOfChangeLamp("10") }}
    <h3>商品排出口</h3>
    {{ output.length ? output : "なし"}}
    <h3>釣り銭排出口</h3>
    {{ change.length ? change : "なし" }}
  </div>
</template>

<script>
import {PRODUCT_TYPES, PRODUCT_PRICE} from '../constants/config.js'

export default {
  name: 'Exterior',
  data(){
    return {
      PRODUCT_TYPES: PRODUCT_TYPES,
      PRODUCT_PRICE: PRODUCT_PRICE,
      power: "OFF",
      inputAmount:"",
      outOfChange:{},
      canBuyNow: {},
      outOfStock: {},
      output: [],
      change: []
    }
  },
  computed: {
    inputAmountYen: function() {
      return this.inputAmount ? (this.inputAmount + "円") : "--";
    },
    outOfChangeLamp: function() {
      return function(coin) {
        return this.outOfChange[coin] ? this.outOfChange[coin] : "--";
      }
    },
  },
  methods: {
    powerOn() {
      if(this.power === "OFF") {
        this.power = "ON";
        return true;
      }
      return false;
    },
    isPowerOn() {
      return this.power === "ON";
    },
    getInputAmount() {
      return parseInt(this.inputAmount, 10);
    },
    setInputAmount(inputAmount) {
      this.inputAmount = inputAmount.toString();
    },
    setOutOfChange(hasEnoughChange) {
      let obj = {}
      for(let coin in hasEnoughChange) {
        obj[coin] = hasEnoughChange[coin] ? "OFF": "ON";
      }
      this.outOfChange = Object.assign({}, obj);
    },
    setCanBuyNow(canBuyNow) {
      this.canBuyNow = canBuyNow;
    },
    setOutOfStock(outOfStock) {
      this.outOfStock = outOfStock;
    },
    setOutput(output) {
      this.output = output;
    },
    setChange(change) {
      this.change = change;
    },
    // 購入可能ランプ
    getCanBuyLamp(canBuyNow){
      if(canBuyNow === undefined || !this.isPowerOn()) {
        return "--";
      } else {
        return canBuyNow ? "ON" : "OFF";
      }
    },
    // 品切れランプ
    getOutOfStockLamp(outOfStock) {
      if(outOfStock === undefined || !this.isPowerOn()) {
        return "--";
      } else {
        return outOfStock ? "ON" : "OFF";
      }
    },
    returnCoin(coin) {
      this.change.push(coin);
    },
    serveProduct(product) {
      this.output.push(product);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 20px 0 0;
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
