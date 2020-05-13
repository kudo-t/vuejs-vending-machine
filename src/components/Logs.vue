<template>
  <div>
    <h1>ログ</h1>
    <hr>
    <pre id="log_area"></pre>
  </div>
</template>

<script>
export default {
  name: 'Logs',
  mounted: function () {
    this.printLog("INFO ", "清涼飲料水自動販売機にようこそ。");
  },
  data() {
    return {
      count: 0
    };
  },
  methods: {
    debug(message) {
      this.printLog("DEBUG", message);
    },
    info(message) {
      this.printLog("INFO ", message);
    },
    warn(message) {
      this.printLog("WARN ", message);
    },
    error(message) {
      this.printLog("ERROR", message);
    },
    printLog(log_level, message) {
      if(this.count >= 42) {
        document.getElementById("log_area").innerHTML = "";
        this.count = 0;
      }
      document.getElementById("log_area").innerHTML += (this.generateLogDate() + " ["+ log_level + "] " + message + "\n");
      this.count++;
    },
    generateLogDate() {
      let date = new Date();
      
      let fillZero = function( number ) {
        return ("0" + number).slice(-2);
      };
      let month = date.getMonth() + 1;
      let day = fillZero(date.getDate());
      let hours = fillZero(date.getHours());
      let minutes = fillZero(date.getMinutes());
      let seconds = fillZero(date.getSeconds());
      
      return month + "/" + day + " " + hours + ":" + minutes + ":" + seconds;
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
pre {
  width: 520px;
  line-height: 1.1;
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
