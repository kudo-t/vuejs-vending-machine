<template>
  <div class="grid-container">
    <div class="grid-item">
      <Exterior ref="exterior" 
        :stockProducts="stockProducts"
        :canBuyNow="canBuyNow"
      />
    </div>
    <div class="grid-item">
      <Interior ref="interior" 
        :stockProducts="stockProducts"
      />
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
import {FLOWCHARTS} from './constants/messages.js'

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
      stockProducts:
      {
        "A": 2,
        "B": 2
      },
      canBuyNow:
      {
        "A": false,
        "B": false
      },
      output:"",
      change:[]
    };
  },
  methods: {
    sleep() {
      return new Promise(resolve => setTimeout(resolve, 1000));
    },
    async switchOn() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId, FLOWCHARTS.SWITCH_ON);
      
      // １．電源スイッチをONにする
      if(isContinue){
        await this.sleep();
        if(this.$refs.exterior.powerOn()) {
          this.$refs.logs.info("[" + eventId + "]" + "電源スイッチをONに切り替えました。");
        } else {
          this.$refs.logs.warn("[" + eventId + "]" + "電源スイッチは既にONです。");
          isContinue = false;
        }
      }
      
      if(isContinue){
        // ２．投入済硬貨の初期化
        await this.sleep();
        // ２－１．投入済硬貨のスキャン
        this.$refs.interior.initializeInputCoins();
        this.$refs.logs.info("[" + eventId + "]" + "投入済硬貨をスキャンしました。");
        await this.sleep();
        // ２－２．投入金額を表示
        let amount = this.$refs.interior.getInputAmount();
        this.$refs.exterior.setInputAmount(amount);
        this.$refs.logs.info("[" + eventId + "]" + "投入金額の表示を更新しました。");
        
        // ３．硬貨在庫の初期化
        await this.sleep();
        // ３－１．硬貨在庫のスキャン
        this.$refs.interior.initializeStockCoins();
        this.$refs.logs.info("[" + eventId + "]" + "硬貨在庫をスキャンしました。");
        await this.sleep();
        // ３－２．釣り銭切れランプ表示
        let isEnoughChange = this.$refs.interior.isEnoughChange();
        this.$refs.exterior.setOutOfChangeLamp(isEnoughChange);
        this.$refs.logs.info("[" + eventId + "]" + "釣り銭切れランプの表示を更新しました。");
        
        // ４．商品在庫の初期化
        await this.sleep();
        // ４－１．商品在庫のスキャン
        this.$refs.interior.initializeProducts();
        this.$refs.logs.info("[" + eventId + "]" + "商品在庫をスキャンしました。");
        await this.sleep();
        // ４－２．購入可能/品切れランプ表示
        this.$refs.exterior.initializeProducts();
        this.$refs.logs.info("[" + eventId + "]" + "購入可能/品切れランプの表示を更新しました。");
      }
      
      this.endFlow(eventId,FLOWCHARTS.SWITCH_ON);
    },
    insertCoin(coin) {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId,FLOWCHARTS.INSERT_COIN + ": " + coin);
      
      if(isContinue){
        this.$refs.logs.warn("[" + eventId + "]" + "本機能は未実装です。");
      }
      this.endFlow(eventId,FLOWCHARTS.INSERT_COIN + ": " + coin);
    },
    purchaseA() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId,FLOWCHARTS.PURCHASE_A);
      
      if(isContinue){
        this.$refs.logs.warn("[" + eventId + "]" + "本機能は未実装です。");
      }
      this.endFlow(eventId,FLOWCHARTS.PURCHASE_A);
    },
    purchaseB() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId,FLOWCHARTS.PURCHASE_B);
      
      if(isContinue){
        this.$refs.logs.warn("[" + eventId + "]" + "本機能は未実装です。");
      }
      this.endFlow(eventId,FLOWCHARTS.PURCHASE_B);
    },
    returnCoins() {
      let eventId = ++this.eventId;
      let isContinue = this.startFlow(eventId, FLOWCHARTS.RETURN_COINS);
      
      if(isContinue){
        this.$refs.logs.warn("[" + eventId + "]" + "本機能は未実装です。");
      }
      this.endFlow(eventId, FLOWCHARTS.RETURN_COINS);
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
    endFlow(eventId, flow_name) {
      this.$refs.logs.info("[" + eventId + "]" + flow_name + " 終了");
      if(this.loading === eventId) {
        this.loading = -1;
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
  grid-template-columns: auto auto auto auto;
}
.grid-item {
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 20px;
}
</style>
