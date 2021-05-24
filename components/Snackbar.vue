<template>
  <v-snackbar v-model="show" :color="color" timeout="3000">
    <h3>{{ message }}</h3>
    <template #action="{ attrs }">
      <v-btn text v-bind="attrs" @click="snackbar = false">CLOSE</v-btn>
    </template>
  </v-snackbar>
</template>

<script>
export default {
  data() {
    return {
      show: false,
      message: '',
      color: '',
    }
  },

  created() {
    this.$store.subscribe((mutation, state) => {
      if (mutation.type === 'snackbar/showMessage') {
        this.message = state.snackbar.content
        this.color = state.snackbar.color
        this.show = true
      }
    })
  },
}
</script>
