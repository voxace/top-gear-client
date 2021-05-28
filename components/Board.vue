<template>
  <v-card>
    <v-toolbar flat>
      <v-toolbar-title> {{ car }} @ {{ track }} </v-toolbar-title>
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
          <td class="pl-2">{{ FormatTime(item) }}</td>
        </template>
        <template v-if="admin" #[`item.remove`]="{ item }">
          <td>
            <v-btn x-small color="error" icon @click="RemoveTime(item._id)"
              ><v-icon>mdi-delete</v-icon>
            </v-btn>
          </td>
        </template>
      </v-data-table>
    </v-card-text>
    <v-card-actions v-if="admin">
      <v-spacer />
      <v-btn small plain color="error" class="mr-4" @click="RemoveBoard(id)"
        >REMOVE
      </v-btn>
    </v-card-actions>
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
    admin: {
      type: Boolean,
      required: true,
      default: false,
    },
  },
  data() {
    return {
      Times: [],
    }
  },
  mounted() {
    this.$nextTick(() => {
      if (process.browser) {
        this.GetTimes()
        this.timer = setInterval(this.GetTimes, 15000)
      }
    })
  },
  computed: {
    times() {
      return this.Times.map((items, index) => ({
        ...items,
        index: index + 1,
      }))
    },
    headers() {
      if (this.admin) {
        return [
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
            width: '120px',
            sortable: false,
          },
          {
            text: '',
            value: 'remove',
            align: 'center',
            width: '20px',
            sortable: false,
          },
        ]
      } else {
        return [
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
            width: '120px',
            sortable: false,
          },
        ]
      }
    },
  },
  methods: {
    async GetTimes() {
      this.Times = await this.$axios.$get(
        '/laptime/leaderboard?leaderboard=' + this.id
      )
    },
    async RemoveTime(id) {
      const vm = this
      await this.$axios
        .$delete('/laptime/' + id)
        .then(() => {
          vm.$notifier.showMessage({
            content: 'Laptime removed successfully',
            color: 'success',
          })
        })
        .catch(() => {
          vm.$notifier.showMessage({
            content: 'Error deleting laptime',
            color: 'error',
          })
        })
      await this.GetTimes()
    },
    async RemoveBoard(id) {
      const vm = this
      await this.$axios
        .$delete('/laptime/leaderboard/' + id)
        .then(async () => {
          await this.$axios
            .$delete('/leaderboard/' + id)
            .then(() => {
              vm.$notifier.showMessage({
                content: 'Leaderboard removed successfully',
                color: 'success',
              })
            })
            .catch(() => {
              vm.$notifier.showMessage({
                content: 'Error deleting laptimes from leaderboard',
                color: 'error',
              })
            })
        })
        .catch(() => {
          vm.$notifier.showMessage({
            content: 'Error deleting leaderboard',
            color: 'error',
          })
        })
      this.$emit('refresh')
    },
    FormatTime(item) {
      return (
        item.minutes +
        ':' +
        item.seconds +
        ':' +
        String(item.ms).padStart(3, '0')
      )
    },
  },
}
</script>

<style>
.v-data-table-header th {
  font-size: 1rem !important;
}
tr:hover {
  background-color: transparent !important;
}
.v-btn i:hover {
  transform: scale(1.25);
}
</style>
