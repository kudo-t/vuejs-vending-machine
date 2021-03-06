<template>
  <div class="grid-container">
    <div class="grid-item">
      <Exterior ref="exterior" />
    </div>
    <div class="grid-item">
      <Interior ref="interior" />
    </div>
    <div class="grid-item">
      <Actions
        v-on:switch-on="switchOn"
        v-on:insert-coin="insertCoin"
        v-on:purchase="purchase"
        v-on:return-coins="returnCoins"
      /><br><br><br>
      <Settings 
        v-on:set-wait="setWait"
      />
    </div>
    <div class="grid-item">
      <Logs ref="logs" />
    </div>
  </div>
</template>

<script>
import Exterior from './components/Exterior.vue'
import Interior from './components/Interior.vue'
import Actions from './components/Actions.vue'
import Settings from './components/Settings.vue'
import Logs from './components/Logs.vue'
import {FLOWCHART_NAME_JP, FLOWCHART_NAME_EN} from './constants/messages.js'

export default {
  name: 'App',
  components: {
    Exterior,
    Interior,
    Actions,
    Settings,
    Logs
  },
  data(){
    return {
      eventId: 0,
      loading: -1,
      wait: 1000,
      FLOWCHARTS: {
        SWITCH_ON: {
          first: {
            func: this.powerOn,
            onSuccess: "initializeInputCoins",
            onFailure: ""
          },
          initializeInputCoins: {
            func: this.initializeInputCoins,
            onSuccess: "setInputAmount"
          },
          setInputAmount: {
            func: this.setInputAmount,
            onSuccess: "initializeStockCoins"
          },
          initializeStockCoins: {
            func: this.initializeStockCoins,
            onSuccess: "setOutOfChangeLamp"
          },
          setOutOfChangeLamp: {
            func: this.setOutOfChangeLamp,
            onSuccess: "initializeProducts"
          },
          initializeProducts: {
            func: this.initializeProducts,
            onSuccess: "setOutOfStockLamp"
          },
          setOutOfStockLamp: {
            func: this.setOutOfStockLamp,
            onSuccess: "setCanBuyNow"
          },
          setCanBuyNow: {
            func: this.setCanBuyNow,
            onSuccess: ""
          }
        },
        INSERT_COIN: {
          first: {
            func: this.acceptCoin,
            onSuccess: "addInputCoin",
            onFailure: "returnCoin"
          },
          returnCoin: {
            func: this.returnCoin,
            onSuccess: ""
          },
          addInputCoin: {
            func: this.addInputCoin,
            onSuccess: "setInputAmount"
          },
          setInputAmount: {
            func: this.setInputAmount,
            onSuccess: "setCanBuyNow"
          },
          setCanBuyNow: {
            func: this.setCanBuyNow,
            onSuccess: ""
          }
        },
        PURCHASE: {
          first: {
            func: this.acceptPurchase,
            onSuccess: "serveProduct",
            onFailure: ""
          },
          serveProduct: {
            func: this.serveProduct,
            onSuccess: "returnChange"
          },
          returnChange: {
            func: this.returnChange,
            onSuccess: "setCanBuyNow",
            onFailure: "returnChange"
          },
          setCanBuyNow: {
            func: this.setCanBuyNow,
            onSuccess: "setOutOfStockLamp"
          },
          setOutOfStockLamp: {
            func: this.setOutOfStockLamp,
            onSuccess: "setOutOfChangeLamp"
          },
          setOutOfChangeLamp: {
            func: this.setOutOfChangeLamp,
            onSuccess: ""
          }
        },
        RETURN_COINS: {
          first: {
            func: this.returnInputCoin,
            onSuccess: "setCanBuyNow",
            onFailure: "setInputAmount"
          },
          setInputAmount: {
            func: this.setInputAmount,
            onSuccess: "first"
          },
          setCanBuyNow: {
            func: this.setCanBuyNow,
            onSuccess: ""
          }
        }
      }
    };
  },
  methods: {
    sleep() {
      return new Promise(resolve => setTimeout(resolve, this.wait));
    },
    setWait(wait) {
      this.wait = wait;
    },
    switchOn() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId, FLOWCHART_NAME_JP.SWITCH_ON);
      
      if(isContinue) {
        isContinue = this.proceedFlow(eventId, FLOWCHART_NAME_EN.SWITCH_ON);
      } else {
        this.endFlow(eventId,FLOWCHART_NAME_JP.SWITCH_ON);
      }
    },
    insertCoin(coin) {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId,FLOWCHART_NAME_JP.INSERT_COIN + ": " + coin);
      
      if(isContinue){
        isContinue = this.proceedFlow(eventId, FLOWCHART_NAME_EN.INSERT_COIN, coin);
      } else {
        this.endFlow(eventId,FLOWCHART_NAME_JP.INSERT_COIN + ": " + coin);
      }
    },
    purchase(product) {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId,FLOWCHART_NAME_JP.PURCHASE + ": " + product);
      
      if(isContinue){
        isContinue = this.proceedFlow(eventId, FLOWCHART_NAME_EN.PURCHASE, product);
      } else {
        this.endFlow(eventId,FLOWCHART_NAME_JP.PURCHASE + ": " + product);
      }
    },
    returnCoins() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId, FLOWCHART_NAME_JP.RETURN_COINS);
      
      if(isContinue) {
        isContinue = this.proceedFlow(eventId, FLOWCHART_NAME_EN.RETURN_COINS);
      } else {
        this.endFlow(eventId,FLOWCHART_NAME_JP.RETURN_COINS);
      }
    },
    startFlow(eventId, flow_name) {
      this.$refs.logs.info("[" + eventId + "]" + flow_name + " 開始");
      if(this.loading >= 0){
        this.$refs.logs.warn("[" + eventId + "]" + "処理中のため、操作がキャンセルされました。");
        return false;
      }
      this.loading = eventId;
      return true;
    },
    async proceedFlow(eventId, flow_name, param) {
      let flow = this.FLOWCHARTS[flow_name];
      let nextFunction = flow["first"];
      
      while(nextFunction) {
        await this.sleep();
        if(nextFunction["func"](eventId, param)) {
          nextFunction = flow[nextFunction["onSuccess"]];
        } else {
          nextFunction = flow[nextFunction["onFailure"]];
        }
      }
      this.endFlow(eventId, FLOWCHART_NAME_JP[flow_name]);
    },
    endFlow(eventId, flow_name) {
      this.$refs.logs.info("[" + eventId + "]" + flow_name + " 終了");
      if(this.loading === eventId) {
        this.loading = -1;
      }
    },
    powerOn(eventId) {
      if(this.$refs.exterior.powerOn()) {
        this.$refs.logs.info("[" + eventId + "]" + "電源スイッチをONに切り替えました。");
        return true;
      } else {
        this.$refs.logs.warn("[" + eventId + "]" + "電源スイッチは既にONです。");
        return false;
      }
    },
    acceptCoin(eventId, coin) {
      if(!this.$refs.exterior.isPowerOn()) {
        this.$refs.logs.warn("[" + eventId + "]" + "電源が入っていません。");
        return false;
      }
      
      if(!this.$refs.interior.canAcceptCoin(coin)) {
        this.$refs.logs.warn("[" + eventId + "]" + "無効な硬貨です。: " + coin);
        return false;
      }
      
      if(!this.$refs.interior.canAddInputCoin(coin)) {
        this.$refs.logs.warn("[" + eventId + "]" + "既に十分な金額が投入されています。");
        return false;
      }
      
      this.$refs.logs.info("[" + eventId + "]" + "硬貨を受け付けました。: " + coin);
      return true;
      
    },
    returnCoin(eventId, coin) {
      // 硬貨返却
      this.$refs.exterior.returnCoin(coin);
      this.$refs.logs.info("[" + eventId + "]" + "硬貨を返却しました。: " + coin);
      
      return true;
    },
    initializeInputCoins(eventId) {
      // 投入済硬貨のスキャン
      this.$refs.interior.initializeInputCoins();
      this.$refs.logs.info("[" + eventId + "]" + "投入済硬貨を初期化しました。");
      
      return true;
    },
    addInputCoin(eventId, coin) {
      this.$refs.interior.addInputCoin(coin);
      this.$refs.logs.info("[" + eventId + "]" + "投入済硬貨を更新しました。");
      
      return true;
    },
    setInputAmount(eventId) {
      // 投入金額を表示
      let amount = this.$refs.interior.getInputAmount();
      this.$refs.exterior.setInputAmount(amount);
      this.$refs.logs.info("[" + eventId + "]" + "投入金額の表示を更新しました。: " + amount);
      
      return true;
    },
    initializeStockCoins(eventId) {
      // 硬貨在庫のスキャン
      this.$refs.interior.initializeStockCoins();
      this.$refs.logs.info("[" + eventId + "]" + "硬貨在庫をスキャンしました。");
      
      return true;
    },
    setOutOfChangeLamp(eventId) {
      // 釣り銭切れランプ表示
      let hasEnoughChange = this.$refs.interior.getHasEnoughChange();
      this.$refs.exterior.setOutOfChange(hasEnoughChange);
      this.$refs.logs.info("[" + eventId + "]" + "釣り銭切れランプの表示を更新しました。");
      
      return true;
    },
    initializeProducts(eventId) {
      // 商品在庫のスキャン
      this.$refs.interior.initializeProducts();
      this.$refs.logs.info("[" + eventId + "]" + "商品在庫をスキャンしました。");

      return true;
    },
    setOutOfStockLamp(eventId) {
      // 品切れランプ更新
      let outOfStock = this.$refs.interior.getOutOfStock();
      this.$refs.exterior.setOutOfStock(outOfStock);
      this.$refs.logs.info("[" + eventId + "]" + "品切れランプを更新しました。");
      
      return true;
    },
    setCanBuyNow(eventId) {
      // 購入可能ランプ更新
      let canBuyNow = this.$refs.interior.getCanBuyNow();
      this.$refs.exterior.setCanBuyNow(canBuyNow);
      this.$refs.logs.info("[" + eventId + "]" + "購入可能ランプを更新しました。");
      
      return true;
    },
    returnInputCoin(eventId) {
      // 投入済硬貨を返却
      let coin = this.$refs.interior.returnInputCoin();
      if(coin) {
        this.$refs.exterior.returnCoin(coin);
        this.$refs.logs.info("[" + eventId + "]" + "硬貨を返却しました。: " + coin);
        return false;
      } else {
        this.$refs.logs.info("[" + eventId + "]" + "投入済硬貨がありません。");
        return true;
      }
    },
    acceptPurchase(eventId, product) {
      if(this.$refs.exterior.isPowerOn()) {
        if(this.$refs.interior.canBuyProduct(product)) {
          this.$refs.logs.info("[" + eventId + "]" + "購入できます。");
          return true;
        }
        this.$refs.logs.warn("[" + eventId + "]" + "購入できません。");
      } else {
        this.$refs.logs.warn("[" + eventId + "]" + "電源が入っていません。");
      }
      return false;
    },
    serveProduct(eventId, product) {
      // 投入金額に、商品金額を引いた値をセット
      let amount = this.$refs.interior.getPurchasedInputAmount(product);
      this.$refs.exterior.setInputAmount(amount);
      this.$refs.logs.info("[" + eventId + "]" + "投入金額の表示を更新しました。: " + amount);
      
      // 投入済硬貨を硬貨在庫へ格納
      this.$refs.interior.storeInputCoins();
      this.$refs.logs.info("[" + eventId + "]" + "投入済硬貨を硬貨在庫へ格納しました。");
      
      // 商品を排出
      this.$refs.interior.serveProduct(product);
      this.$refs.exterior.serveProduct(product);
      this.$refs.logs.info("[" + eventId + "]" + "商品を排出しました。: " + product);
      
      return true;
    },
    returnChange(eventId) {
      let amount = this.$refs.exterior.getInputAmount();
      
      let coin = this.$refs.interior.returnChange(amount);
      if(coin) {
        this.$refs.exterior.returnCoin(coin);
        
        amount = amount - parseInt(coin, 10);
        this.$refs.exterior.setInputAmount(amount);
        this.$refs.logs.info("[" + eventId + "]" + "釣り銭を排出しました。: " + coin);
        
        if(amount === 0) {
          this.$refs.logs.info("[" + eventId + "]" + "釣り銭はすべて排出済です。");
          return true;
        }
        return false;
      } else {
        this.$refs.logs.error("[" + eventId + "]" + "予期せぬエラーが発生しました。");
        return true;
      }
    }
  },
}
</script>

<style>
.grid-container {
  font-family: "Source Sans Pro", "Helvetica Neue", Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  display:grid;
  grid-template-columns: 322px auto auto auto;
}
.grid-item {
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 20px;
}
</style>
