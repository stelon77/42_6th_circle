<template>
  <div>
    <div v-if="myrelation.type == 0">
      <v-btn color="#f5cac3" class="mt-6" @click="askFriends()">
        add to my friends
        <v-icon color="#395c6b" right>
          fa-user-plus
        </v-icon>
      </v-btn>
      <v-btn color="#f5cac3" class="mt-6" @click="blockFriends()">
        block this user
        <v-icon color="#395c6b" right>
          fa-user-times
        </v-icon>
      </v-btn>
    </div>
    <!-- invitation sent -->
    <div v-if="myrelation.type == 1">
      <v-btn color="#f5cac3" disabled class="mt-6" @click="deleteFriends()">
        invitation sent
        <v-icon color="#395c6b" right>
          fas fa-paper-plane
        </v-icon>
      </v-btn>
      <v-btn color="#f5cac3" class="mt-6" @click="deleteFriends()">
        delete invitation
        <v-icon color="#395c6b" right>
          fas fa-times
        </v-icon>
      </v-btn>
    </div>
    <!-- invitation received -->
    <div v-if="myrelation.type == 2">
      <v-row justify="center" align="center">
        <v-card class="our_dark_beige pa-md-4">
          you received an invitation from this user !
          <v-btn color="#f5cac3" class="mb-2 mt-2 ml-2 mr-2" @click="becomeFriends()">
            become friend
            <v-icon color="#395c6b" right>
              fas fa-user-check
            </v-icon>
          </v-btn>
          <v-btn color="#f5cac3" class="mb-2 mt-2 ml-2 mr-2" @click="deleteFriends()">
            delete invitation
            <v-icon color="#395c6b" right>
              fas fa-trash-alt
            </v-icon>
          </v-btn>
        </v-card>
      </v-row>
    </div>
    <!-- we are friends -->
    <div v-if="myrelation.type == 3">
      <v-btn color="#f5cac3" class="mt-6" :to="`/dm/${getNameDM()}`">
        send a message
        <v-icon color="#395c6b" right>
          fa-comment-alt
        </v-icon>
      </v-btn>
      <v-row justify="center" align="center" class="mt-5">
        <v-btn color="#f5cac3" class="mt-6" @click="deleteFriends()">
          Unfriend
          <v-icon color="#395c6b" right>
            fa-user-minus
          </v-icon>
        </v-btn>
      </v-row>
      <v-btn color="#f5cac3" class="mt-6" @click="blockFriends()">
        block this user
        <v-icon color="#395c6b" right>
          fa-user-times
        </v-icon>
      </v-btn>
    </div>
    <!-- blocked -->
    <div v-if="myrelation.type == 4">
      <v-btn color="#EDEDED" disabled class="mt-6" @click="deleteFriends()">
        you have blocked this user
        <v-icon color="#395c6b" right>
          fa-ban
        </v-icon>
      </v-btn>
      <v-btn color="#EDEDED" class="mt-6" @click="deleteFriends()">
        unblock
      </v-btn>
    </div>
    <div v-if="alertBlocked == true">
      <v-alert
        type="error"
        class="mt-6"
      >
        Sorry you most likely have been blocked by this user
      </v-alert>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { meStore, relationshipStore, usersStore } from '~/store';
import { User } from '~/models/user'
import { Relationship, RelationshipType } from '~/models';

export default Vue.extend({

  layout: 'default',
  props: ['userId'],
  data: () => ({
    alertBlocked: false,
  }),

  computed: {
    myrelation (): Relationship {
      return relationshipStore.one(this.userId);
    },
    users (): User[] {
      return usersStore.allUsers;
    },
    me (): User {
      return meStore.me;
    },
  },

  methods: {
    async askFriends () {
      try {
        await this.$axios.$post(`relationships/${this.userId}`, { type: RelationshipType.sent }, { withCredentials: true })
      } catch {
        this.alertBlocked = true;
      }
    },
    async becomeFriends () {
      try {
        await this.$axios.$post(`relationships/${this.userId}`, { type: RelationshipType.friend }, { withCredentials: true })
      } catch {
        this.alertBlocked = true;
      }
    },
    async deleteFriends () {
      try {
        await this.$axios.$delete(`relationships/${this.userId}`, { withCredentials: true })
      } catch {
        this.alertBlocked = true;
      }
    },
    async blockFriends () {
      try {
        await this.$axios.$post(`relationships/${this.userId}`, { type: RelationshipType.blocked }, { withCredentials: true })
      } catch {
        this.alertBlocked = true;
      }
    },
    getNameDM () {
      const name = this.me.id + ':' + this.userId;
      return name;
    }
  }
})

</script>

<style scoped lang="scss">
#component-logo {
  margin-top: 25px;
  i{
    font-size: 32px;
  }
}
</style>
