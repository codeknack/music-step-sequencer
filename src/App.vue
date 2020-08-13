<template>
  <v-app>
    <v-card dark width="580" max-width="580" class="mx-auto">
      <v-toolbar color="grey darken-3">
        <v-icon class="orange--text text--lighten-1 mr-3">mdi-piano</v-icon>
        <v-toolbar-title class="blue--text headline">Music Step Sequencer</v-toolbar-title>
      </v-toolbar>
      <!-- Add <SoundTracks></SoundTracks> component here-->
      <SoundTracks 
        :tracks="tracks" 
        @playsound="playSound" 
        @mutesound="muteSound"
      ></SoundTracks>
      <!-- Add <SoundControls></SoundControls> component here-->
      <SoundControls 
        :tempo.sync="tempo" 
        :counter="counter" 
        :isPlaying="isPlaying"
        @play="play" 
        @stop="stop" 
      ></SoundControls>
      <!-- Add <SoundPresets></SoundPresets> component here-->
      <SoundPresets 
        :currentPreset="currentPreset"
        @cleartracks="clearTracks"
        @loadpreset="loadPreset" 
      ></SoundPresets>
    </v-card>
  </v-app>
</template>

<script>
import { Howl } from "howler";
import SoundTracks from "./components/SoundTracks.vue";
import SoundControls from "./components/SoundControls.vue";
import SoundPresets from "./components/SoundPresets.vue";

const kick = new Howl({src: ["https://raw.githubusercontent.com/codeknack/music-step-sequencer/master/src/assets/sounds/kick.mp3",],});
const snare = new Howl({src: ["https://raw.githubusercontent.com/codeknack/music-step-sequencer/master/src/assets/sounds/snare.mp3",],});
const hihat = new Howl({src: ["https://raw.githubusercontent.com/codeknack/music-step-sequencer/master/src/assets/sounds/hihat.mp3",],});
const shaker = new Howl({src: ["https://raw.githubusercontent.com/codeknack/music-step-sequencer/master/src/assets/sounds/shaker.mp3",],});

let audioContext = new AudioContext();

export default {
  name: 'App',

  components: {
    SoundTracks,
    SoundControls,
    SoundPresets
  },

  data() {
    return {
      tempo: 120,
      tracks: {
        kick: [],
        snare: [],
        hihat: [],
        shaker: [],
      },
      futureTickTime: audioContext.currentTime,
      counter: 0,
      timerID: null,
      isPlaying: false,      
    };
  },

  computed: {
    secondsPerBeat() {
      return 60 / this.tempo;
    },
    counterTimeValue() {
      return this.secondsPerBeat / 4;
    },
    currentPreset() {
      return {
        tempo: this.tempo,
        tracks: this.tracks
      };
    }
  },

  methods: {
    scheduleSound(trackArray, sound, counter) {
      for (var i = 0; i < trackArray.length; i += 1) {
        if (counter === trackArray[i]) {
          sound.play();
        }
      }
    },
    playTick() {
      this.counter += 1;
      this.futureTickTime += this.counterTimeValue;

      if (this.counter > 15) {
        this.counter = 0;
      }
    },
    scheduler() {
      if (this.futureTickTime < audioContext.currentTime + 0.1) {
        this.scheduleSound(this.tracks.kick, kick, this.counter);
        this.scheduleSound(this.tracks.snare, snare, this.counter);
        this.scheduleSound(this.tracks.hihat, hihat, this.counter);
        this.scheduleSound(this.tracks.shaker, shaker, this.counter);
        this.playTick();
      }

      this.timerID = window.setTimeout(this.scheduler, 0);
    },
    play() {
      if (this.isPlaying === false) {
        this.counter = 0;
        this.futureTickTime = audioContext.currentTime;
        this.scheduler();
        this.isPlaying = true;
      } 
    },
    stop() {
      if (this.isPlaying === true) {
        window.clearTimeout(this.timerID);
        this.isPlaying = false;
      }
    },
    playSound(sound) {
      eval(sound).play()
    },
    muteSound(obj) {
      eval(obj['sound']).mute(!obj['toggle'])
    },
    clearTracks() {
      this.tracks =  {
        kick: [],
        snare: [],
        hihat: [],
        shaker: [],
      }
    },
    loadPreset(preset) {
      let presets = JSON.parse(localStorage.getItem('userPresets'));
      this.tempo = presets[preset].tempo;
      this.tracks = presets[preset].tracks;
    },
  }
};
</script>
