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
        <tr v-for="product in products" v-bind:key="product.name">
          <td>{{ product.name }}</td>
          <td>{{ product.price + "円"}}</td>
          <td>{{ getCanBuyLamp(product.canBuyNow) }}</td>
          <td>{{ getOutOfStockLamp(product.amount) }}</td>
        </tr>
      </tbody>
    </table>
    <h3>投入金額</h3>
    {{ inputAmount }}
    <h3>釣り銭切れランプ</h3>
    {{ outOfChangeLamp }}
    <h3>商品排出口</h3>
    {{ output.length ? output : "なし"}}
    <h3>釣り銭排出口</h3>
    {{ change.length ? change : "なし" }}
  </div>
</template>

<script>
export default {
  name: 'Exterior',
  props: {
   products: Array
  },
  data(){
    return {
      power: "OFF",
      productsInitialized: false,
      inputAmount:"--",
      outOfChangeLamp:"--",
      output: [],
      change: []
    }
  },
  methods: {
    powerOn() {
      if(this.power === "OFF") {
        this.power = "ON";
        return true;
      }
      return false;
    },
    initializeProducts() {
      this.productsInitialized = true;
    },
    setInputAmount(inputAmount) {
      this.inputAmount = inputAmount + "円";
    },
    setOutOfChangeLamp(isEnoughChange) {
      this.outOfChangeLamp = isEnoughChange ? "OFF": "ON";
    },
    setOutput(output) {
      this.output = output;
    },
    setChange(change) {
      this.change = change;
    },
    // 購入可能ランプ
    getCanBuyLamp(canBuyNow){
      if(!this.productsInitialized) {
        return "--";
      } else {
        return canBuyNow ? "ON" : "OFF";
      }
    },
    // 品切れランプ
    getOutOfStockLamp(amount) {
      if(!this.productsInitialized) {
        return "--";
      } else {
        return amount === 0 ? "ON" : "OFF";
      }
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
