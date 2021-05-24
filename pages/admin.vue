<template>
  <v-row justify="center" align="start">
    <v-col :cols="12">
      <v-img src="TOP-GEAR-HEADER.png" />
    </v-col>
    <v-col cols="6">
      <AddLeaderboard />
    </v-col>
    <v-col cols="6">Add Laptime</v-col>
  </v-row>
</template>

<script>
import AddLeaderboard from '@/components/AddLeaderboard'

export default {
  components: {
    AddLeaderboard,
  },
}
</script>
