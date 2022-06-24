<template>
  <v-data-table
    :headers="headers"
    :items="city"
    sort-by="_id"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Event</v-toolbar-title>
        <v-spacer></v-spacer>
      </v-toolbar>
    </template>
    <template #[`item.place`]="{ item }">
      <div class="py-2">
        <img width="100" :src="item.place.thumbnail" :alt="item.place.title" />
        <div>{{ item.place.title }}</div>
      </div>
    </template>
  </v-data-table>
</template>

<script>
import Delete from '~/components/City/Delete.vue'
export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    city: [],
    headers: [
      {
        text: 'User',
        align: 'start',
        sortable: false,
        value: 'user._id',
      },
      { text: 'Place', value: 'place' },
      { text: 'Description', value: 'desc' },
      { text: 'Requirement', value: 'requirement' },
    ],
  }),
  created() {
    this.get()
  },

  methods: {
    get() {
      this.$axios.get('/event/all').then((res) => {
        console.log('suggestion', res.data.data)
        this.city = res.data.data.docs
      })
    },
  },
  components: { Delete },
}
</script>
