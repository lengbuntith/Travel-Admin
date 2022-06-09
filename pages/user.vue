<template>
  <v-data-table
    :headers="headers"
    :items="user"
    sort-by="_id"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>USER CRUD</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
      </v-toolbar>
    </template>
    <template #[`item.thumbnail`]="{ item }">
      <img width="100" :src="item.thumbnail" :alt="item.name" />
    </template>

    <template #[`item.actions`]="{ item }">
      <Delete :id="item._id" />
    </template>
  </v-data-table>
</template>

<script>
import Delete from '~/components/User/Delete.vue'
export default {
  data: () => ({
    user: [],
    headers: [
      {
        text: 'ID',
        align: 'start',
        sortable: false,
        value: '_id',
      },
      { text: 'Name', value: 'name' },
      { text: 'Email', value: 'email' },
      { text: 'Role', value: 'role' },
      { text: 'Actions', value: 'actions' },
    ],
  }),
  created() {
    this.get()
    this.$nuxt.$on('getUser', () => {
      this.get()
    })
  },

  beforeDestroy() {
    this.$nuxt.$off('getUser')
  },

  methods: {
    get() {
      this.$axios.get('/auth/users/all').then((res) => {
        console.log('user', res.data.data)
        this.user = res.data.data
      })
    },
  },
  components: { Delete },
}
</script>
