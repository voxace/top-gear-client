<template>
  <v-row justify="center" align="top">
    <v-col :cols="12">
      <v-img src="TOP-GEAR-HEADER.png" />
    </v-col>
    <v-col v-for="(leaderboard, n) in leaderboards" :key="n">
      <Board
        :id="leaderboard._id"
        :car="leaderboard.car"
        :track="leaderboard.track"
      />
    </v-col>
  </v-row>
</template>

<script>
import Board from '@/components/Board'

export default {
  components: {
    Board,
  },
  data() {
    return {
      loading: false,
      Leaderboards: [],
    }
  },
  computed: {
    leaderboards() {
      return this.Leaderboards
    },
  },
  created() {
    if (process.browser) {
      this.GetBoards()
    }
  },
  methods: {
    async GetBoards() {
      this.loading = true
      this.Leaderboards = await this.$axios.$get('/leaderboard')
      // eslint-disable-next-line no-console
      console.log(this.Leaderboards)
      setTimeout(() => {
        this.loading = false
      }, 100)
    },
  },
}
</script>
