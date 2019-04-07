<template>
  <div id="app">
    <HelloWorld v-bind:msg="status"/>
    <datetime placeholder="When to remind" type="datetime" v-model="date"></datetime>
    <textarea v-model="message" placeholder="What to remind"></textarea>
    <div>
      <button id="submit" v-on:click="submitForm">Add</button>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import HelloWorld from './components/HelloWorld.vue'

function start(websocketServerLocation) {
    this.ws = new WebSocket(window.AppConfig.SOCKET_URL);
    this.ws.onclose = function(){
      setTimeout(function(){start(websocketServerLocation)}, 5000);
    };
}

export default {
  name: 'app',
  data: function() {
    return {
      status: 'Add a reminder',
      message: '',
      date: moment().add('hour', 1).format(),
    }
  },
  mounted: function() {start.call(this)},
  methods: {
    submitForm: function(e) {
      const apiUrl = window.AppConfig.API_URL
      const chatId = window.AppConfig.CHAT_ID

      if (!apiUrl || !chatId) {
        console.error('API_URL and CHAT_ID are mandatory')
        return
      }


      this.ws.send(JSON.stringify({
        chat: chatId,
        message: this.message,
        date: this.date
      }))

      this.$set(this, 'message', '')
      this.$set(this, 'date', moment().add('hour', 1).format())
      this.status = 'Successfully added'
    }
  },
  components: {
    HelloWorld
  }
}
</script>

<style>
body {margin: 0;}
#submit {
  border: 3px solid #2c3e50;
  border-radius: 5px;
  background: none;
  font-size: 25px;
  padding: 10px 20px;
  outline: none;
  cursor: pointer;
}
#submit:hover {
  background-color: #42b983;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.vdatetime-input,
#app textarea {
  margin-bottom: 25px;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  font-size: 25px;
  padding: 15px 25px;
  width: 350px;
  max-width: 100%;
  color: #2c3e50;
  border: 3px solid #2c3e50;
  border-radius: 5px;
  outline: none;
}
</style>
