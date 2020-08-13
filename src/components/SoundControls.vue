<template>
  <div>
    <div class="d-flex justify-space-between">
      <div class="ml-2">
        <v-btn @click="play">
          <v-icon class="orange--text">mdi-play</v-icon>
        </v-btn>
        <v-btn @click="stop" class="ml-2">
          <v-icon class="orange--text">mdi-stop</v-icon>
        </v-btn>
      </div>
      <div class="d-flex align-center">
        <v-icon class="mr-3 blue--text">mdi-metronome-tick</v-icon>
        <v-rating
          full-icon="mdi-metronome"
          empty-icon="mdi-metronome-tick"
          color="green" background-color="grey darken-3"
          length="4" readonly
          v-model="metronome"
        ></v-rating>
      </div>
    </div>
    <div>
      <v-slider 
        class="ml-3" color="light-green" dense
        max="180" min="60" step="10"
        v-model="localTempo"
        :label="'Tempo: ' + localTempo + ' BPM'" 
        @click="updateTempo"
      ></v-slider> 
    </div>
  </div> 
</template>

<script>
export default {
  props: ['tempo', 'counter', 'isPlaying'],

  data() {
    return {
      localTempo: this.tempo,
      metronome: 0,
    };
  },

  watch: {
    counter(val) {
      if (this.isPlaying) {
        if (val >= 0 && val <= 3) {
          this.metronome = 1;
        } else if (val > 3 && val <= 7) {
          this.metronome = 2;
        } else if (val > 7 && val <= 11) {
          this.metronome = 3;
        } else if (val > 11 && val <= 15) {
          this.metronome = 4;
        } 
      }
    },
    tempo(val) {
      this.localTempo = val;
    }
  },

  methods: {
    play() {
      this.$emit("play");
    },
    stop() {
      this.$emit("stop");
      this.metronome = 0;
    },
    updateTempo() {
      this.$emit("update:tempo", this.localTempo);
    },
  },
}
</script>