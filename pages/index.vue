<template>
  <div class="pa-4">
    <v-row>
      <v-col v-for="(overview, index) in overviews" :key="index">
        <v-card
          height="100"
          class="d-flex align-center pa-4"
          :color="overview.color"
        >
          <div class="mr-2">
            <v-icon color="white" size="50">{{ overview.icon }}</v-icon>
          </div>
          <div class="white--text">
            <div class="font-weight-bold">
              {{ overview.name }}
            </div>
            <div class="font-weight-bold">
              {{ overview.value }}
            </div>
          </div>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      overviews: [
        {
          name: 'Place',
          icon: 'mdi-map',
          value: '0',
          color: 'green',
        },
        {
          name: 'Event',
          icon: 'mdi-group',
          value: '0',
          color: 'red',
        },
        {
          name: 'Suggestion',
          icon: 'mdi-chat',
          value: '0',
          color: 'orange',
        },
        {
          name: 'User',
          icon: 'mdi-account-group',
          value: '0',
          color: 'blue',
        },
      ],
    }
  },

  created() {
    this.$axios.get('/place/all').then((res) => {
      this.overviews[0].value = res.data.data.totalDocs
    })

    this.$axios.get('/event/all').then((res) => {
      this.overviews[1].value = res.data.data.totalDocs
    })

    this.$axios.get('/suggestion/all').then((res) => {
      this.overviews[2].value = res.data.data.totalDocs
    })

    this.$axios.get('/auth/users/all').then((res) => {
      this.overviews[3].value = res.data.data.length
    })
  },
}
</script>

<style lang="scss" scoped></style>
