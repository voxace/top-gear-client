<template>
  <v-row justify="center" align="start">
    <v-col :cols="12">
      <v-img src="TOP-GEAR-HEADER.png" />
    </v-col>
    <v-col cols="6">
      <AddLeaderboard />
    </v-col>
    <v-col cols="6">
      <AddLaptime />
    </v-col>
  </v-row>
</template>

<script>
import AddLeaderboard from '@/components/AddLeaderboard'
import AddLaptime from '@/components/AddLaptime'

export default {
  components: {
    AddLeaderboard,
    AddLaptime,
  },
}
</script>
