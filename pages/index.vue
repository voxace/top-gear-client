<template>
  <v-row justify="center" align="start">
    <v-col :cols="12">
      <v-img src="TOP-GEAR-HEADER.png" />
    </v-col>
    <template v-if="leaderboards.length > 0">
      <v-col v-for="(leaderboard, n) in leaderboards" :key="n">
        <Board
          :id="leaderboard._id"
          :car="leaderboard.car"
          :track="leaderboard.track"
          :admin="admin"
          @refresh="GetBoards()"
        />
      </v-col>
    </template>
    <template v-else>
      <v-col :cols="12" class="text-center mt-16">
        <h2>To get started, add a leaderboard</h2>
      </v-col>
    </template>
    <v-col :cols="12">
      <!-- ADD BUTTON -->
      <v-btn
        class="mb-12 mr-2"
        fab
        absolute
        bottom
        right
        dark
        @click.stop="dialog = true"
      >
        <v-icon dark>mdi-plus</v-icon>
      </v-btn>

      <!-- UNLOCK ADMIN BUTTON -->
      <v-btn
        v-if="!admin"
        class="mb-12 mr-2"
        fab
        absolute
        bottom
        left
        dark
        @click.stop="openAdminDialog()"
      >
        <v-icon dark>mdi-lock-open</v-icon>
      </v-btn>

      <!-- LOCK ADMIN BUTTON -->
      <v-btn
        v-if="admin"
        class="mb-12 mr-2"
        fab
        absolute
        bottom
        left
        dark
        @click.stop="lockAdmin()"
      >
        <v-icon dark>mdi-lock</v-icon>
      </v-btn>

      <!-- ADD DATA DIALOG -->
      <v-dialog v-model="dialog" persistent max-width="1200px">
        <v-card class="pa-4">
          <v-card-text>
            <v-container v-if="dialog">
              <v-row justify="center" align="start">
                <v-col v-if="admin">
                  <AddLeaderboard @close="closeDialog()" />
                </v-col>
                <v-col>
                  <AddLaptime
                    :leaderboards="leaderboards"
                    @close="closeDialog()"
                  />
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="dialog = false"
              >CANCEL</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- UNLOCK ADMIN DIALOG -->
      <v-dialog v-model="adminDialog" persistent max-width="600px">
        <v-card class="pa-4">
          <v-card-title>Unlock Admin Mode</v-card-title>
          <v-card-text>
            <v-container v-if="adminDialog">
              <v-row justify="center" align="start">
                <v-col cols="12">Enter the administrator password:</v-col>
                <v-col cols="12">
                  <v-text-field
                    ref="adminInput"
                    v-model="adminPassword"
                    type="password"
                  />
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="unlockAdmin()"
              >UNLOCK</v-btn
            >
            <v-btn color="blue darken-1" text @click="adminDialog = false"
              >CANCEL</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-col>
  </v-row>
</template>

<script>
import Board from '@/components/Board'
import AddLeaderboard from '@/components/AddLeaderboard'
import AddLaptime from '@/components/AddLaptime'

export default {
  components: {
    Board,
    AddLeaderboard,
    AddLaptime,
  },
  data() {
    return {
      Leaderboards: [],
      timer: '',
      dialog: false,
      adminDialog: false,
      admin: false,
      adminPassword: '',
    }
  },
  computed: {
    leaderboards() {
      return this.Leaderboards
    },
  },
  mounted() {
    this.$nextTick(() => {
      this.$nuxt.$loading.start()
      if (process.browser) {
        this.GetBoards()
        this.timer = setInterval(this.GetBoards, 120000) // refresh boards every 2 mins
      }
      setTimeout(() => this.$nuxt.$loading.finish(), 500)
    })
  },
  destroyed() {
    clearInterval(this.timer)
  },
  methods: {
    async GetBoards() {
      this.$nuxt.$loading.start()
      this.Leaderboards = await this.$axios.$get('/leaderboard')
      setTimeout(() => this.$nuxt.$loading.finish(), 1000)
    },
    openAdminDialog() {
      this.adminDialog = true
      setTimeout(() => {
        this.$refs.adminInput.$refs.input.focus()
      }, 200)
    },
    closeDialog() {
      this.dialog = false
      this.Leaderboards = []
      this.GetBoards()
    },
    unlockAdmin() {
      if (this.adminPassword === '123') {
        this.admin = true
        this.adminDialog = false
        this.adminPassword = ''
        this.$notifier.showMessage({
          content: 'Admin mode unlocked',
          color: 'success',
        })
      } else {
        this.$notifier.showMessage({
          content: 'Incorrect password',
          color: 'error',
        })
      }
    },
    lockAdmin() {
      this.admin = false
      this.$notifier.showMessage({
        content: 'Admin mode locked',
        color: 'success',
      })
    },
  },
}
</script>
