<template>
  <v-container fill-height>
    <v-row class="mt-4 mb-n7 ml-1">
      <h1> Your Stats : </h1>
    </v-row>
    <v-row>
      <v-col
        sm="5"
        class="mr-10"
        align="center"
        justify="center"
      >
        <v-card
          class="pa-2 mb-7 mt-7 our_navy_blue--text"
          color="our_dark_beige"
          outlined
          align="center"
          max-width="500px"
        >
          <h3>
            Level : {{ getLevel() }}
          </h3>
        </v-card>

        <v-card
          class="scroll mb-7 mt-7"
          color="our_dark_beige"
          height="374px"
          align="center"
          max-width="600px"
          justify="center"
        >
          <v-card-title class="our_beige our_navy_blue--text justify-center">
            Achievements
          </v-card-title>
          <achivements :user-id="me.id" />
        </v-card>
      </v-col>
      <v-col
        sm="5"
        class="ml-10"
        align="center"
        justify="center"
      >
        <v-card
          class="pa-2 mb-7 mt-7 our_navy_blue--text"
          color="our_dark_beige"
          outlined
          align="center"
          max-width="500px"
        >
          <h3>
            Victories : {{ me.victories }}  / {{ getMatches() }}
          </h3>
        </v-card>
        <v-card
          class="pa-2 mb-7 mt-7 our_navy_blue--text"
          color="our_dark_beige"
          outlined
          align="center"
          max-width="500px"
        >
          <h3>
            Defeats : {{ me.defeats }}  / {{ getMatches() }}
          </h3>
        </v-card>

        <match-history :user-id="me.id" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue'
import matchHistory from '~/components/stats/matchHistory.vue';
import achivements from '~/components/stats/achivements.vue';
import { meStore } from '~/store'
import { User } from '~/models/user'

export default Vue.extend({
  components: { matchHistory, achivements },
  layout: 'default',
  data () {
    return {
      matches: Object(),
    };
  },

  computed: {
    me (): User {
      return meStore.me;
    },

  },

  async mounted () {
    meStore.fetch();
    this.matches = await this.$axios.$get(`user/matches/${this.me.id}`, { withCredentials: true });
  },

  methods: {

    getVictories () {
      let e = 0;
      for (let i = 0; i < this.matches.length; i++) {
        if (this.matches[i].user1Score > this.matches[i].user2Score) { e++; }
      }
      return e;
    },

    getLosses () {
      let e = 0;
      for (let i = 0; i < this.matches.length; i++) {
        if (this.matches[i].user2Score > this.matches[i].user1Score) { e++; }
      }
      return e;
    },

    getMatches () {
      return (this.matches.length)
    },

    getLevel () {
      const win = this.getVictories();
      if (win === 0) { return 0 } else { return (win * 0.4); }
    },
  },
})

</script>

<style scoped lang="scss">
.scroll {
   overflow-y: scroll
}
</style>
