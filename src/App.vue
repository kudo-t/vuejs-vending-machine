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
        v-on:purchase-A="purchaseA"
        v-on:purchase-B="purchaseB"
        v-on:return-coins="returnCoins"
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
import Logs from './components/Logs.vue'
import {FLOWCHART_NAME_JP, FLOWCHART_NAME_EN} from './constants/messages.js'

export default {
  name: 'App',
  components: {
    Exterior,
    Interior,
    Actions,
    Logs
  },
  data(){
    return {
      eventId: 0,
      loading: -1,
      FLOWCHARTS : {
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
        }
      }
    };
  },
  methods: {
    sleep() {
      return new Promise(resolve => setTimeout(resolve, 1000));
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
        // １．電源スイッチ確認 new
        
        
        // ２．投入物体受付 new
        
        // ３．投入済硬貨の更新
        // ３－１．投入済硬貨の追加 new
        // ３－２．投入金額を表示 既存
        
        
        // ４．購入可能表示更新 new
        
        // ３’．投入物体返却 new
        
      }
      this.endFlow(eventId,FLOWCHART_NAME_JP.INSERT_COIN + ": " + coin);
    },
    purchaseA() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId,FLOWCHART_NAME_JP.PURCHASE_A);
      
      if(isContinue){
        this.$refs.logs.warn("[" + eventId + "]" + "本機能は未実装です。");
      }
      this.endFlow(eventId,FLOWCHART_NAME_JP.PURCHASE_A);
    },
    purchaseB() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId,FLOWCHART_NAME_JP.PURCHASE_B);
      
      if(isContinue){
        this.$refs.logs.warn("[" + eventId + "]" + "本機能は未実装です。");
      }
      this.endFlow(eventId,FLOWCHART_NAME_JP.PURCHASE_B);
    },
    returnCoins() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId, FLOWCHART_NAME_JP.RETURN_COINS);
      
      if(isContinue){
        this.$refs.logs.warn("[" + eventId + "]" + "本機能は未実装です。");
      }
      this.endFlow(eventId, FLOWCHART_NAME_JP.RETURN_COINS);
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
    async proceedFlow(eventId, flow_name) {
      let flow = this.FLOWCHARTS[flow_name];
      let nextFunction = flow["first"];
      
      while(nextFunction) {
        await this.sleep();
        if(nextFunction["func"](eventId)) {
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
    initializeInputCoins(eventId) {
      // 投入済硬貨のスキャン
      this.$refs.interior.initializeInputCoins();
      this.$refs.logs.info("[" + eventId + "]" + "投入済硬貨を初期化しました。");
      
      return true;
    },
    setInputAmount(eventId) {
      // 投入金額を表示
      let amount = this.$refs.interior.getInputAmount();
      this.$refs.exterior.setInputAmount(amount);
      this.$refs.logs.info("[" + eventId + "]" + "投入金額の表示を更新しました。");
      
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
      let isEnoughChange = this.$refs.interior.isEnoughChange();
      this.$refs.exterior.setOutOfChangeLamp(isEnoughChange);
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
  grid-template-columns: auto auto auto auto;
}
.grid-item {
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 20px;
}
</style>
