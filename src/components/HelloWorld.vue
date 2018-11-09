<template>
    <div id="app">

        <lottie :options="defaultOptions" :height="400" :width="400" v-on:animCreated="handleAnimation"/>
        <div>
            <p>BPM: {{bpm}}</p>
            <input type="range" value="1" min="0" max="240" step="1"
                   v-on:change="onSpeedChange" v-model="bpm">
        </div>
        <button v-on:click="stop">stop</button>
        <button v-on:click="pause">pause</button>
        <button v-on:click="play">play</button>
    </div>
</template>

<script>
  import Lottie from 'vue-lottie';
  import animationData from '../assets/tryout1.json';

  export default {
    name: 'app',
    components: {
      'lottie': Lottie
    },
    data() {
      return {
        defaultOptions: {animationData: animationData},
        animationSpeed: 1,
        bpm: 120
      }
    },
    
    methods: {
      handleAnimation: function (anim) {
        this.anim = anim;
      },

      stop: function () {
        this.anim.stop();
      },

      play: function () {
        this.anim.play();
      },

      pause: function () {
        this.anim.pause();
      },

      onSpeedChange: function () {
        this.animationSpeed = this.bpm / 120;
        this.anim.setSpeed(this.animationSpeed);
      }
    }
  }
</script>
