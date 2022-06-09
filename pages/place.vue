<template>
  <v-data-table
    :headers="headers"
    :items="places"
    sort-by="_id"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>PLACE CRUD</v-toolbar-title>
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
import Add from '~/components/Place/Add.vue'
import Delete from '~/components/Place/Delete.vue'
import Edit from '~/components/Place/Edit.vue'
export default {
  data: () => ({
    places: [],
    headers: [
      {
        text: 'Thumbnail',
        align: 'start',
        sortable: false,
        value: 'thumbnail',
      },
      { text: 'Title', value: 'title' },
      { text: 'Story', value: 'story' },
      { text: 'Actions', value: 'actions' },
    ],
  }),
  created() {
    this.get()
    this.$nuxt.$on('getPlace', () => {
      this.get()
    })
  },

  beforeDestroy() {
    this.$nuxt.$off('getPlace')
  },

  methods: {
    get() {
      this.$axios.get('/place/all?page=1&select=story').then((res) => {
        console.log('place', res.data.data)
        this.places = res.data.data.docs
      })
    },
  },
  components: { Add, Delete, Edit },
}
</script>
