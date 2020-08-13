<template>
<v-container>
  <template v-for="(track, name) in tracks" > 
    <v-row :key="name" align="center" dense>
      <v-col cols="1">
        <v-switch inset dense color="light-green" 
          v-model="toggles[name]" 
          @change="muteSound(name, toggles[name])"
        ></v-switch>
      </v-col>
      <v-col cols="1">
        <v-btn fab raised small @click="playSound(name)">
          <v-icon class="green--text" v-show="toggles[name]">mdi-volume-high</v-icon>
          <v-icon class="grey--text" v-show="!toggles[name]">mdi-volume-off</v-icon>
        </v-btn>
      </v-col>
      <v-col cols="10">         
        <v-btn-toggle  v-model="tracks[name]" multiple>
          <v-btn color="lime accent-4" height="40" small icon 
            v-for="n in 16" :key="n"
          ></v-btn>
        </v-btn-toggle>
      </v-col>
    </v-row>
  </template>
</v-container>
</template>

<script>
export default {
  props: ["tracks"],

  data() {
    return {
      toggles: {
        kick: true,
        snare: true,
        hihat: true,
        shaker: true,
      },
    };
  },

  methods: {
    playSound(sound) {
      this.$emit('playsound', sound);
    },
    muteSound(sound, toggle) {
      this.$emit('mutesound', {sound, toggle});
    }
  }
}
</script>