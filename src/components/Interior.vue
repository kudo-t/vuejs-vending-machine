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
        <tr v-for="inputCoin in inputCoins" v-bind:key="inputCoin.name">
          <td>{{ inputCoin.name }}</td>
          <td>{{ inputCoinsInitialized ? inputCoin.amount : "--"}}</td>
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
        <tr v-for="stockCoin in stockCoins" v-bind:key="stockCoin.name">
          <td>{{ stockCoin.name }}</td>
          <td>{{ stockCoinsInitialized ? stockCoin.amount : "--"}}</td>
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
        <tr v-for="product in products" v-bind:key="product.name">
          <td>{{ product.name }}</td>
          <td>{{ productsInitialized ? product.amount : "--"}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'Interior',
  props: {
    products: Array
  },
  data() {
    return {
      inputCoinsInitialized: false,
      stockCoinsInitialized: false,
      productsInitialized: false,
      inputCoins:
      [
        {
          name: "500",
          amount: 0
        },
        {
          name: "100",
          amount: 0
        },
        {
          name: "50",
          amount: 0
        },
        {
          name: "10",
          amount: 0
        }
      ],
      stockCoins:
      [
        {
          name: "500",
          amount: 0
        },
        {
          name: "100",
          amount: 99
        },
        {
          name: "50",
          amount: 99
        },
        {
          name: "10",
          amount: 99
        }
      ]
    };
  },
  methods : {
    getAmount(coins) {
      let amount = 0;
      for (let i in coins) {
        amount += parseInt(coins[i]["name"], 10) * coins[i]["amount"];
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
      // 100円以上の硬貨で400円が作れ、
      // 10円以上の硬貨で90円が作れれば、釣り銭充足と判定
      // 100円以上のお釣りを50/10円硬貨で返却させない
      return true;
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
