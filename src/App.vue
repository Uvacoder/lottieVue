<template>
  <div id="app">
  
  {{ me }}
    <button @click="login">Login with Spotify</button>

    <HelloWorld />
  </div>
</template>

<script>


  

import HelloWorld from './components/HelloWorld.vue'
// import { firebase } from 'firebase';



export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data() {
    return {
      client_id: 'fef0c38afdaa4991b99105485070a86b',
      scopes: 'user-top-read',
      redirect_uri: 'https://blissful-minsky-31dfc6.netlify.com',
      me: null
    }
  },
    methods: {
    login() {
      let popup = window.open(`https://accounts.spotify.com/authorize?client_id=${this.client_id}&response_type=token&redirect_uri=${this.redirect_uri}&scope=${this.scopes}&show_dialog=true`, 'Login with Spotify', 'width=800,height=600')
      
      window.spotifyCallback = (payload) => {        
        popup.close()
        
        fetch('https://api.spotify.com/v1/me', {
          headers: {
            'Authorization': `Bearer ${payload}`
          }
        }).then(response => {
          console.log(response)
          return response.json()
        }).then(data => {
          this.me = data
        })
      }
    }
  },
  mounted() {
    this.token = window.location.hash.substr(1).split('&')[0].split("=")[1]
    
    if (this.token) {
      window.opener.spotifyCallback(this.token)
    }
  }
}
</script>

<style lang="scss">
body {
  background-color: #ffd862;
} 
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  
}
</style>
