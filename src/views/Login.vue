<template>
  <div class="login">
    <img alt="Chat App Logo" src="../assets/logo.png">
    <p>
      <button @click="socialLogin" class="social-button">Sign In with Google</button>
      <br><br>
      <input type="text" v-model="username" placeholder="Enter nickname" @keydown.space.prevent><br>
      <button @click="anonLogin">Sign In Anonymously</button>
    </p>
  </div>
</template>

<script>
  import firebase from '../config/firebase';

  export default {
    name: 'login',
    data() {
      return {
        username: '',
        uid: ''
      }
    },
    methods: {
      socialLogin() {
        const provider = new firebase.auth.GoogleAuthProvider()
        firebase.auth().signInWithPopup(provider).then((result) => {
          this.username = result.user.displayName
          this.uid = result.user.uid

          this.$router.push({name: 'Chat', params: {username: this.username, uid: this.uid}})
        }).catch((err) => {
          alert('Oops. ' + err.message)
        })
      },
      anonLogin() {
        if(this.username) {
          firebase.auth().signInAnonymously().then((result) =>{
            this.uid = result.user.uid

            this.$router.push({name: 'Chat', params: {username: this.username, uid: this.uid}})
          }).catch((err) => {
            alert('Oops. ' + err.message)
          })
        }else {
          alert('Nickname not given!')
        }
      }
    }
  }
</script>

<style scoped>  /* "scoped" attribute limit the CSS to this component only */
  .login {
    width: 50%;
    margin: 0;
    position: absolute;
    top: 50%;
    left: 50%;
    -ms-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    border: 5px solid #000;
  }

  img {
    width: 30%;
  }

  input {
    margin: 10px 0 5px;
    width: 20%;
    padding: 10px;
    border: 1px solid #555;
  }

  button {    
    width: auto;
    height: 50px;
    border-radius: 12px;
    box-shadow: 0 2px 4px 0 rgba(0,0,0,0.2);
    outline: 0;
    border: 0;
    cursor: pointer;
  }

  p {
    margin-top: 0;
    font-size: 16px;
  }

  .social-button {
    background-image: url(../assets/google-logo.png);
    background-size: 30px;
    background-repeat: no-repeat;
    background-position: 10px 10px;
    padding: 20px 15px 20px 45px;
  }
</style>