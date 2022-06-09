<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list height="100%" color="#57BCAD">
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
          class="white--text"
        >
          <v-list-item-action>
            <v-icon color="white">{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>

        <v-list-item @click="$auth.logout()" class="white--text">
          <v-list-item-action>
            <v-icon color="white">mdi-logout</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="'Logout'" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title v-text="title" />
      <v-spacer />
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>

    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: true,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Dashboard',
          to: '/',
        },
        {
          icon: 'mdi-account-group',
          title: 'User',
          to: '/user',
        },
        {
          icon: 'mdi-city',
          title: 'City',
          to: '/city',
        },
        {
          icon: 'mdi-map',
          title: 'Place',
          to: '/place',
        },
        {
          icon: 'mdi-chat',
          title: 'Suggestion',
          to: '/suggestion',
        },

        {
          icon: 'mdi-group',
          title: 'Event',
          to: '/event',
        },

        {
          icon: 'mdi-tools',
          title: 'Setting',
          to: '/setting',
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Travel Advisor',
    }
  },
}
</script>
