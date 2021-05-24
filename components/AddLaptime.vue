<template>
  <v-form v-model="boardValid" class="pr-4">
    <v-container>
      <v-row>
        <v-col cols="12">
          <h2>Add Laptime</h2>
        </v-col>
        <v-col cols="12">
          <v-text-field v-model="name" label="Name" required></v-text-field>
        </v-col>
        <v-col cols="4">
          <v-text-field
            v-model.number.lazy="minutes"
            label="Minutes"
            type="number"
            min="0"
            max="59"
            required
            @input="validateMinutes"
          ></v-text-field>
        </v-col>
        <v-col cols="4">
          <v-text-field
            v-model.number.lazy="seconds"
            label="Seconds"
            type="number"
            min="0"
            max="59"
            required
            @input="validateSeconds"
          ></v-text-field>
        </v-col>
        <v-col cols="4">
          <v-text-field
            v-model.number.lazy="ms"
            label="Milliseconds"
            type="number"
            min="0"
            max="999"
            required
            @input="validateMillis"
          ></v-text-field>
        </v-col>
        <v-col cols="12">
          <v-select
            v-model="leaderboard"
            :items="leaderboards"
            item-value="_id"
            label="Leaderboard"
            required
          >
            <template slot="selection" slot-scope="data">
              {{ data.item.car }} @ {{ data.item.track }}
            </template>
            <template slot="item" slot-scope="data">
              {{ data.item.car }} @ {{ data.item.track }}
            </template>
          </v-select>
        </v-col>
        <v-col cols="12" class="text-right">
          <v-btn class="mr-4" :disabled="invalid" @click="SaveLaptime"
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
      Leaderboards: [],
      snackbarText: 'Leaderboard Saved',
      timeout: 3000,
      snackbar: true,
      boardValid: false,
      name: '',
      minutes: 0,
      seconds: 0,
      ms: 0,
      leaderboard: '',
      timer: '',
    }
  },
  computed: {
    invalid() {
      if (this.name === '' || this.track === '') {
        return true
      }
      return false
    },
    leaderboards() {
      return this.Leaderboards
    },
  },
  created() {
    if (process.browser) {
      this.GetBoards()
      this.timer = setInterval(this.GetBoards, 30000)
    }
  },
  destroyed() {
    clearInterval(this.timer)
  },
  methods: {
    validateMinutes() {
      if (this.minutes < 0) {
        setTimeout(() => {
          this.minutes = 0
        }, 100)
        this.$notifier.showMessage({
          content: 'Minutes must be 0 or greater',
          color: 'error',
        })
      } else if (this.minutes > 59) {
        setTimeout(() => {
          this.minutes = 59
        }, 100)
        this.$notifier.showMessage({
          content: 'Minutes must be 59 or less',
          color: 'error',
        })
      }
    },
    validateSeconds() {
      if (this.seconds < 0) {
        setTimeout(() => {
          this.seconds = 0
        }, 100)
        this.$notifier.showMessage({
          content: 'Seconds must be 0 or greater',
          color: 'error',
        })
      } else if (this.minutes > 59) {
        setTimeout(() => {
          this.seconds = 59
        }, 100)
        this.$notifier.showMessage({
          content: 'Seconds must be 59 or less',
          color: 'error',
        })
      }
    },
    validateMillis() {
      if (this.ms < 0) {
        setTimeout(() => {
          this.ms = 0
        }, 100)
        this.$notifier.showMessage({
          content: 'Milliseconds must be 0 or greater',
          color: 'error',
        })
      } else if (this.ms > 999) {
        setTimeout(() => {
          this.ms = 999
        }, 100)
        this.$notifier.showMessage({
          content: 'Milliseconds must be 999 or less',
          color: 'error',
        })
      }
    },
    GetLeaderboardText(_id) {
      return 'test'
    },
    async GetBoards() {
      this.Leaderboards = await this.$axios.$get('/leaderboard')
      // eslint-disable-next-line no-console
      console.log(this.Leaderboards)
    },
    async SaveLaptime() {
      const vm = this
      await this.$axios
        .$post('/laptime', {
          name: this.name,
          minutes: this.minutes,
          seconds: this.seconds,
          ms: this.ms,
          leaderboard: this.leaderboard,
        })
        .then(() => {
          vm.$notifier.showMessage({
            content: 'Laptime saved successfully',
            color: 'success',
          })
          this.car = ''
          this.track = ''
        })
        .catch(() => {
          vm.$notifier.showMessage({
            content: 'Error saving laptime',
            color: 'error',
          })
        })
    },
  },
}
</script>
