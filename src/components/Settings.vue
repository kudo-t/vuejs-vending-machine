<template>
  <div class="settings">
    <h1>設定</h1>
    <hr>
    <h3>処理速度</h3>
    <input v-model="wait" type="radio" id="w1" @change="onChangeWait($event)" value="1000">
    <label for="w1">&nbsp;Slow&nbsp;</label>
    <input v-model="wait" type="radio" id="w2" @change="onChangeWait($event)" value="500">
    <label for="w2">&nbsp;Medium&nbsp;</label>
    <input v-model="wait" type="radio" id="w3" @change="onChangeWait($event)" value="0">
    <label for="w3">&nbsp;Fast&nbsp;</label><br>
  </div>
</template>

<script>
export default {
  name: 'Settings',
  data() {
    return {
      wait: "1000"
    };
  },
  methods: {
    onChangeWait(event) {
      this.$emit("set-wait", event.target.value); 
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 20px 0 0;
}
</style>
