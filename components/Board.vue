<template>
  <v-card>
    <v-toolbar flat>
      <v-toolbar-title>{{ track }} - {{ car }}</v-toolbar-title>
    </v-toolbar>
    <v-card-text>
      <v-data-table
        :items="times"
        :loading="loading"
        :headers="headers"
        :hide-default-footer="true"
      >
        <template #[`item.num`]="{ item }">
          <td>{{ item.index }}</td>
        </template>
        <template #[`item.name`]="{ item }">
          <td>{{ item.name }}</td>
        </template>
        <template #[`item.laptime`]="{ item }">
          <td>{{ FormatTime(item) }}</td>
        </template>
      </v-data-table>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  props: {
    car: {
      type: String,
      required: true,
      default: 'Car',
      class: 'table-heading',
    },
    track: {
      type: String,
      required: true,
      default: 'Track',
    },
    id: {
      type: String,
      required: true,
      default: '',
    },
  },
  data() {
    return {
      loading: false,
      Times: [],
      headers: [
        {
          text: '#',
          value: 'num',
          align: 'center',
          width: '40px',
          sortable: false,
        },
        {
          text: 'Name',
          value: 'name',
          align: 'left',
          sortable: false,
        },
        {
          text: 'Lap Time',
          value: 'laptime',
          align: 'right',
          width: '100px',
          sortable: false,
        },
      ],
    }
  },
  computed: {
    times() {
      return this.Times.map((items, index) => ({
        ...items,
        index: index + 1,
      }))
    },
  },
  created() {
    if (process.browser) {
      this.GetTimes()
    }
  },
  methods: {
    async GetTimes() {
      this.loading = true
      this.Times = await this.$axios.$get(
        '/laptime/leaderboard?leaderboard=' + this.id
      )
      setTimeout(() => {
        this.loading = false
      }, 100)
    },
    FormatTime(item) {
      return item.minutes + ':' + item.seconds + ':' + item.ms
    },
  },
}
</script>

<style>
.v-data-table-header th {
  font-size: 16px !important;
}
</style>
