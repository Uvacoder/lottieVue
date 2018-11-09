<template>
  <div id="app">
    <div v-if="loggedIn !== true">
    <button @click="login">Login with Spotify</button>
    </div>
 <!-- <input v-model="song" placeholder="Search for a song"> -->
    <div v-else>
      <button @click="searchSong('20J6w3tFHTlsQi5WyUjK2E')">Search</button>
      <HelloWorld ref="lottie" :tempo="tempo"/>
    </div>
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
      redirect_uri: 'https://blissful-minsky-31dfc6.netlify.com/',
      // redirect_uri: 'http://localhost:8080/',
      me: null,
      search: null,
      songs: null,
      payload: null,
      tempo: 120,
      loggedIn: false
      
    }
  },
    methods: {

    login() {
      let popup = window.open(`https://accounts.spotify.com/authorize?client_id=${this.client_id}&response_type=token&redirect_uri=${this.redirect_uri}&scope=${this.scopes}&show_dialog=true`, 'Login with Spotify', 'width=800,height=600')
      
      window.spotifyCallback = (payload) => {  
        this.payload = payload;      
        popup.close()
        
        fetch('https://api.spotify.com/v1/me', {
          headers: {
            'Authorization': `Bearer ${payload}`
          }
        }).then(response => {
          
          return response.json()
        }).then(data => {
          this.me = data
          this.loggedIn = true;
        })



      }
    },
      searchSongs() {
        
        fetch('https://api.spotify.com/v1/search?q='+this.song+'&type=track&market=US&limit=10&offset=5', {
          headers: {
            'Authorization': 'Bearer ' + this.payload
          }
        }).then(response => {
          
          return response.json()
        }).then(data => {
          
          this.search = data

        })


  },

    searchSong(song) {

       
        fetch('https://api.spotify.com/v1/tracks?ids=' + song, {
          headers: {
            'Authorization': 'Bearer ' + this.payload
          }
        }).then(response => {
          
          return response.json()
        }).then(data => {
          this.songs = data;

           fetch('https://api.spotify.com/v1/audio-features/' + song, {
          headers: {
            'Authorization': 'Bearer ' + this.payload
          }
        }).then(response => {
    
          return response.json()
        }).then(data => {
          
          this.features = data
          this.tempo = data.tempo

          this.play(this.songs.tracks[0].preview_url, data.tempo)

        })




          
          
          
             

          
        })
        
       
  },



    play(song, tempo) {

                var audio = new Audio(song);
          audio.play();
this.$refs.lottie.play();
this.$refs.lottie.setSpeed(tempo)
    
    

  },


  },

  mounted() {
    this.token = window.location.hash.substr(1).split('&')[0].split("=")[1]
    
    if (this.token) {
      window.opener.spotifyCallback(this.token)
      this.spotify.setAccessToken(this.token)

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
