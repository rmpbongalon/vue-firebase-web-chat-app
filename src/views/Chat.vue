<template>
  <div class="chat">
    <div class="msgs-container">
      <div class="messages">
        <div v-for="message in messages" :key="message.id">
          <span class="text-info">[{{ message.username }}]: </span>
          <span>{{message.text}}</span>
          <br>
          <button @click="delmsg(message.uid,message.id)">Delete</button>
        </div>
      </div>
    </div>
    <div class="chat-actions">
      <textarea v-model="inputMessage"></textarea>
      <button @click="send" id="send-btn">Send</button>
      <button @click="logout">Logout</button>
    </div>
  </div>
</template>

<script>
import firebase from '../config/firebase';

const itemsRef = firebase.database().ref("messages");

export default {
  name: 'Chat',
  data() {
    return {
      username: this.$route.params.username,
      inputMessage: '',
      messages: [],
      uid: this.$route.params.uid
    }
  },
  methods: {
    logout() {
      //turnoff listener
      itemsRef.off("value")
      firebase.auth().signOut().then(() => {
        this.$router.replace('login')
      })
    },
    send() {
      if(this.inputMessage.length){
        const message = {
          text: this.inputMessage,
          username: this.username,
          uid: this.uid
        }
        firebase
          .database()
          .ref("messages")
          .push(message)
        this.inputMessage = ""
      }
    },
    delmsg(userid,key) {
      if(userid === this.$route.params.uid) {
        let itemDelRef = itemsRef.child(key)
        itemDelRef.remove()
      }else {
        alert("You can't delete messages sent by other users.")
      }  
    },
  },
  mounted() {
    let viewMessage = this

    itemsRef.on("value", snapshot => {
      let data = snapshot.val()
      let messages = []
      if(data){
        Object.keys(data).forEach(key => {
          messages.push({
            id: key,
            username: data[key].username,
            text: data[key].text,
            uid: data[key].uid
          })
        })
      }
      viewMessage.messages = messages
    })
  }
}
</script>

<style scoped>
  .msgs-container {
    width: 50%;
    min-width: 600px;
    height: 500px;
    margin: 30px auto;
    padding-top: 0.5rem;
    background: #f1f1f1;
    border: 2px solid #cdcdcd;
    overflow: scroll;
  }

  textarea {
    width: 40%;
    min-width: 480px;
    margin: 0 10px;
    resize: none;
    vertical-align: top;
  }

  button {
    height: 30px;
    border-radius: 12px;
    box-shadow: 0 2px 4px 0 rgba(0,0,0,0.2);
    outline: 0;
    border: 0;
    vertical-align: -8px;
    cursor: pointer;
  }

  #send-btn {
    margin-right: 10px;
  }

  .messages div {
    min-width: 10%;
    border-radius: 4px;
    border: 1px solid #000;
    padding-left: 0.5 rem;
    padding-top: 0.25rem;
    margin-left: 0.5rem;
    margin-bottom: 0.5rem;
  }

  .text-info {
    color: blue;
  }
</style>