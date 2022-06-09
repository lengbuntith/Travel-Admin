<template>
  <v-data-table
    :headers="headers"
    :items="city"
    sort-by="_id"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>CITY CRUD</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <Add />
      </v-toolbar>
    </template>
    <template #[`item.thumbnail`]="{ item }">
      <img width="100" :src="item.thumbnail" :alt="item.name" />
    </template>

    <template #[`item.actions`]="{ item }">
      <Edit :id="item._id" />
      <Delete :id="item._id" />
    </template>
  </v-data-table>
</template>

<script>
import Add from '~/components/City/Add.vue'
import Delete from '~/components/City/Delete.vue'
import Edit from '~/components/City/Edit.vue'
export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    city: [],
    headers: [
      {
        text: 'Thumbnail',
        align: 'start',
        sortable: false,
        value: 'thumbnail',
      },
      { text: 'Name', value: 'name' },
      { text: 'Description', value: 'description' },
      { text: 'Actions', value: 'actions' },
    ],
    newCity: {
      name: '',
      thumbnail: '',
      description: '',
    },
  }),
  created() {
    this.get()
    this.$nuxt.$on('getCity', () => {
      this.get()
    })
  },

  beforeDestroy() {
    this.$nuxt.$off('getCity')
  },

  methods: {
    get() {
      this.$axios.get('/city/all').then((res) => {
        console.log('city', res.data.data)
        this.city = res.data.data
      })
    },
  },
  components: { Add, Delete, Edit },
}
</script>
