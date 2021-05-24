<template>
  <v-form v-model="boardValid" class="pr-4">
    <v-container>
      <v-row>
        <v-col cols="12">
          <h2>Add Leaderboard</h2>
        </v-col>
        <v-col cols="12">
          <v-text-field v-model="car" label="Car" required></v-text-field>
        </v-col>
        <v-col cols="12">
          <v-text-field v-model="track" label="Track" required></v-text-field>
        </v-col>
        <v-col cols="12" class="text-right">
          <v-btn class="mr-4" :disabled="invalid" @click="SaveBoard"
            >Submit</v-btn
          >
        </v-col>
      </v-row>
    </v-container>
  </v-form>
</template>

<script>
export default {
  data() {
    return {
      snackbarText: 'Leaderboard Saved',
      timeout: 3000,
      snackbar: true,
      boardValid: false,
      car: '',
      track: '',
    }
  },
  computed: {
    invalid() {
      if (this.car === '' || this.track === '') {
        return true
      }
      return false
    },
  },
  methods: {
    async SaveBoard() {
      const vm = this
      await this.$axios
        .$post('/leaderboard', {
          car: this.car,
          track: this.track,
        })
        .then(() => {
          vm.$notifier.showMessage({
            content: 'Leaderboard saved successfully',
            color: 'success',
          })
          this.car = ''
          this.track = ''
        })
        .catch(() => {
          vm.$notifier.showMessage({
            content: 'Error saving leaderboard',
            color: 'error',
          })
        })
    },
  },
}
</script>
