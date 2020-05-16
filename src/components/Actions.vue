<template>
  <div class="actions">
    <h1>操作</h1>
    <hr>
    <h3>電源スイッチ</h3>
    <button @click="switchOn">ON</button>
    <h3>硬貨投入</h3>
    <input v-model="coin" type="radio" id="c1" value="500">
    <label for="c1">500円玉</label>
    <input v-model="coin" type="radio" id="c2" value="100">
    <label for="c2">100円玉</label>
    <input v-model="coin" type="radio" id="c3" value="50">
    <label for="c3">&nbsp;50円玉</label><br>
    <input v-model="coin" type="radio" id="c4" value="10">
    <label for="c4">&nbsp;10円玉&nbsp;</label>
    <input v-model="coin" type="radio" id="c5" value="5">
    <label for="c5">&nbsp;5円玉&nbsp;</label>
    <input v-model="coin" type="radio" id="c6" value="1">
    <label for="c6">&nbsp;&nbsp;1円玉&nbsp;</label><br>
    <button @click="insertCoin">投入</button>
    <h3>購入ボタン</h3>
    <span v-for="product in PRODUCT_TYPES" v-bind:key="product">
      <button @click="purchase(product)">{{ product }}</button>
    </span>
    <h3>硬貨返却レバー</h3>
    <button @click="returnCoins">押下</button>
  </div>
</template>

<script>
import {PRODUCT_TYPES} from '../constants/config.js'

export default {
  name: 'Actions',
  data() {
    return {
      PRODUCT_TYPES: PRODUCT_TYPES,
      coin: "500"
    };
  },
  methods: {
    switchOn() {
      this.$emit("switch-on");
    },
    insertCoin() {
      this.$emit("insert-coin", this.coin);
    },
    purchase(name) {
      if(name === "A") {
        this.$emit("purchase-A");
      } else if(name ==="B") {
        this.$emit("purchase-B");
      }
    },
    returnCoins() {
      this.$emit("return-coins");
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
