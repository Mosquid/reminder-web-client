<template>
  <div id="app">
    <HelloWorld v-bind:msg="status"/>
    <input type="datetime-local" v-model=date>
    <div>
      <select v-model="chatId" name="" id="">
        <option v-for="chat in chats" v-bind:value="chat.id">{{chat.name}}</option>
      </select>
    </div>
    <textarea v-model="message" placeholder="What to remind"></textarea>
    <div>
      <button id="submit" v-on:click="submitForm">Add</button>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import HelloWorld from './components/HelloWorld.vue'

const format = 'YYYY-MM-DDTHH:mm:ss'

function start() {
  this.$set(this, 'chats', window.AppConfig.CHAT_IDS)
  this.$set(this, 'chatId', this.chats[0].id)
  
  const urlParams = new URLSearchParams(window.location.search);
  const message   = urlParams.get('msg')

  if (message)
    this.$set(this, 'message', message)
}

export default {
  name: 'app',
  data: function() {
    return {
      status: 'Add a reminder',
      message: '',
      chats: '',
      chatId: Number,
      date: moment().add('hour', 1).format(format),
    }
  },
  mounted: function() {start.call(this)},
  methods: {
    submitForm: function(e) {
      const apiUrl = window.location.origin

      if (!apiUrl || !this.chatId) {
        console.error('API_URL and CHAT_ID are mandatory')
        return
      }

      fetch(apiUrl, {
        method: 'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          chat: this.chatId,
          message: this.message,
          date: this.date
        })
      })
      .then(r => r.json())
      .then(res => {
        this.$set(this, 'message', '')
        this.$set(this, 'date', moment().add('hour', 1).format(format))
        this.status = 'Successfully added'

        document.body.classList.add('sent')
        
        setTimeout(function() {
          document.body.classList.remove('sent')
        }, 1000)
      })
      .catch( e => console.log(e))
    }
  },
  components: {
    HelloWorld
  }
}
</script>

<style>
body {margin: 0;}
body.sent {
  background-color: #42b983;
  color: #fff;
}
#submit {
  border: 3px solid #2c3e50;
  border-radius: 5px;
  background: none;
  font-size: 25px;
  padding: 10px 20px;
  outline: none;
  cursor: pointer;
}
select {
  font-size: 150%;
  margin: 10px 10px 30px;
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
input[type="datetime-local"],
#app textarea {
  margin-bottom: 25px;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  font-size: 25px;
  padding: 15px 25px;
  width: 350px;
  max-width: 100%;
  box-sizing: border-box;
  color: #2c3e50;
  border: 3px solid #2c3e50;
  border-radius: 5px;
  outline: none;
}
</style>
