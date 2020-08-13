<template>
  <div>
    <div class="d-flex ma-2">
      <v-btn color="blue" @click="clearTracks">
        Clear Tracks
      </v-btn>
      <v-btn class="ml-2" color="blue" @click.stop="dialog = true">
        Save Preset
      </v-btn>
      <v-spacer></v-spacer>
      <v-btn color="red" @click="deleteAllPresets">
        Delete All Presets
      </v-btn>
    </div>
    <v-card>
      <v-card-title>Presets: {{selectedPreset}}</v-card-title> 
      <v-slide-y-transition>
      <v-card-text v-show="isEmpty(userPresets)" class="grey--text font-italic text-center text-caption">Currently there are no presets created. To create a new preset fill in some cells in the tracks and hit the Save Preset button.</v-card-text>
      </v-slide-y-transition>
      <v-scroll-y-transition>
        <div v-if="!isEmpty(userPresets)">
          <v-chip 
            close close-icon="mdi-delete-forever" 
            @click:close="deletePreset(name)" 
            class="ma-2" color="orange" label
            v-for="(preset, name) in userPresets" :key="name" 
            @click="loadPreset(name)"
          > {{name}}
          </v-chip>
        </div>
      </v-scroll-y-transition>
    </v-card>
    <v-dialog v-model="dialog" max-width="290" light persistent>
      <v-card light rounded>
        <v-card-title class="headline blue--text">Save preset as:</v-card-title>
          <v-text-field
            class="ma-2" label="Preset Name"
            placeholder="Type preset name here"
            v-model="presetName"
            :rules="[rules.required, rules.counter]"
          ></v-text-field>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green lighten-2" text @click="cancelDialog">
            Cancel
          </v-btn>
          <v-btn color="green darken-2" text @click="savePreset" :disabled="!(presetName.length >=3)">
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  created() {
    this.userPresets = JSON.parse(localStorage.getItem('userPresets') || '{}');
  },

  props: ['currentPreset'],

  data() {
    return {
      dialog: false,
      presetName: '',
      rules: {
        required: value => !!value || 'Required.',
        counter: value => value.length >= 3 || 'At least 3 characters required',
      },
      userPresets: {},
      selectedPreset: '',
    };
  },
  methods: {
    clearTracks() {
      this.$emit('cleartracks');
      this.selectedPreset = '';
    },
    loadPreset(preset) {
      this.$emit('loadpreset', preset);
      this.selectedPreset = preset;
    },
    savePreset() {
        this.dialog = false;

        this.userPresets[this.presetName] = {};
        let tracks = Object.assign({}, this.currentPreset.tracks);
        this.userPresets[this.presetName].tempo = this.currentPreset.tempo;
        this.userPresets[this.presetName].tracks = tracks;
        localStorage.setItem('userPresets', JSON.stringify(this.userPresets));

        this.presetName = '';
    },
    cancelDialog() {
      this.dialog = false;
      this.presetName = '';
    },
    deletePreset(preset) {
      this.$delete(this.userPresets, preset);
      localStorage.setItem('userPresets', JSON.stringify(this.userPresets));
      if (preset == this.selectedPreset) {
        this.selectedPreset = '';
      }
    },
    deleteAllPresets() {
      localStorage.clear();
      this.userPresets = {};
    },
    isEmpty(obj) {
      return Object.entries(obj).length === 0;
    }
  }
}
</script>