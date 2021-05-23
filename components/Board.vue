<template>
  <v-card>
    <v-toolbar flat>
      <v-toolbar-title>{{ car }} @ {{ track }}</v-toolbar-title>
    </v-toolbar>
    <v-card-text>
      <v-data-table
        :items="times"
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
        <template #[`item.remove`]="{ item }">
          <td>
            <v-btn x-small color="error" icon @click="RemoveTime(item._id)"
              ><v-icon>mdi-delete</v-icon>
            </v-btn>
          </td>
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
          align: 'center',
          width: '100px',
          sortable: false,
        },
        {
          text: '',
          value: 'remove',
          align: 'center',
          width: '20px',
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
      this.Times = await this.$axios.$get(
        '/laptime/leaderboard?leaderboard=' + this.id
      )
    },
    async RemoveTime(id) {
      await this.$axios.$delete('/laptime', { data: { id } })
      await this.GetTimes()
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
tr:hover {
  background-color: transparent !important;
}
.v-btn i:hover {
  transform: scale(1.25);
}
</style>
