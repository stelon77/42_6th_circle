<template>
  <v-app>
    <v-app-bar
      elevation="0"
      background-color="#395C6B"
      color="#f7ede2"
      dense
      app
      class="pt-2"
    >
      <v-tabs v-model="activeTab" color="#395c6b" centered icons-and-text>
        <v-tab
          to="/profile"
        >
          my profile
        </v-tab>
        <v-tab
          to="/"
        >
          home
        </v-tab>

        <v-tab
          to="/channels"
        >
          Channels
        </v-tab>
        <v-tab to="/game">
          Game
        </v-tab>
      </v-tabs>
      <v-btn text color="#395c6b" @click="logout">
        logout
      </v-btn>
    </v-app-bar>
    <users-bar />

    <v-main style="background-color: #f7ede2">
      <nuxt />
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue'
import UsersBar from '~/components/UsersBar.vue'
import { authenticationStore } from '~/store'

export default Vue.extend({
  components: { UsersBar },
  middleware: ['fetch'],

  data: () => ({
    activeTab: 2
  }),

  head () {
    return {
      title: 'ft-transcendence'
    }
  },
  methods: {
    async logout () {
      await authenticationStore.signOut()
      this.$router.push('/auth')
    }
  }

})
</script>

<style scoped>
.v-application {
  background-color: #f7ede2;
  color: #395c6b;
}

.v-tabs-bar {
  float: right !important;
}

.pp_image {
  height: 5px;
}
</style>
