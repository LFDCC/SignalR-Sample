<template>
  <div class="hello">
    <div class="container">
      <div class="row">&nbsp;</div>
      <div class="row">
        <div class="col-2">User</div>
        <div class="col-4"><input type="text" v-model="userInput" /></div>
      </div>
      <div class="row">
        <div class="col-2">Message</div>
        <div class="col-4"><input type="text" v-model="messageInput" /></div>
      </div>
      <div class="row">&nbsp;</div>
      <div class="row">
        <div class="col-6">
          <input type="button" @click.prevent="sendButton" value="Send Message" :disabled="sendButtonDisable" />
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-12">
        <hr />
      </div>
    </div>
    <div class="row">
      <div class="col-6">
        <ul>
          <li v-for="msg in messagesList" :key="msg">{{msg}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import * as signalR from "@microsoft/signalr";

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      userInput: "",
      messageInput: "",
      messagesList: [],
      connection: null,
      sendButtonDisable:true
    };
  },
  mounted() {
    this.connection = new signalR.HubConnectionBuilder()
      .withUrl("/chatHub")
      .build();
      
    this.connection
      .start()
      .then(()=> {
        this.sendButtonDisable = false;
      })
      .catch(function (err) {
        return window.console.error(err.toString())
      });

    this.connection.on("ReceiveMessage", (user, message) => {
      this.messagesList.push(`${user} says ${message}`);
    });

  },
  methods: {
    sendButton() {
      this.connection
        .invoke("SendMessage", this.userInput, this.messageInput)
        .catch(function (err) {
          return window.console.error(err.toString());
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
</style>
