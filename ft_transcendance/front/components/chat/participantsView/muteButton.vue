<template>
  <div class="text-center">
    <v-dialog
      v-model="dialog"
      width="350"
    >
      <template #activator="{ on, attrs }">
        <v-btn
          v-ripple="false"
          plain
          icon
          title="mute"
          v-bind="attrs"
          v-on="on"
        >
          <v-icon color="#395c6b">
            fa-volume-mute
          </v-icon>
        </v-btn>
      </template>

      <v-card class="our_beige">
        <v-card-title class="our_dark_beige our_navy_blue--text">
          choose time to mute
        </v-card-title>
        <v-select
          v-model="select"
          :items="items"
          label="Type"
          required
          class="mr-3 ml-3"
        />
        <v-row align="center" justify="center">
          <v-btn
            v-if="select"
            color="success"
            class="mt-3 mb-5"
            @click="muteUser"
          >
            Send
          </v-btn>
          <v-btn v-else disabled class="mt-3 mb-5">
            Send
          </v-btn>
        </v-row>
      </v-card>
    </v-dialog>
  </div>
</template>

<script lang="ts">

import Vue from 'vue'

export default Vue.extend({
  props: ['userId', 'channelId'],
  data: () => ({
    items: ['5 minutes', '1 hour', '24 hours', 'until I unmute'],
    select: '',
    dialog: false,
  }),

  methods: {
    async muteUser () {
      const time = this.items.indexOf(this.select)
      const d = new Date();

      if (time === 0) {
        d.setMinutes(d.getMinutes() + 5)
        await this.$axios.$patch(`channel/${this.channelId}/${this.userId}`, { muted: true, muteEnd: d }, { withCredentials: true });
        this.$emit('changeMute');
      } else if (time === 1) {
        d.setHours(d.getHours() + 24)
        await this.$axios.$patch(`channel/${this.channelId}/${this.userId}`, { muted: true, muteEnd: d }, { withCredentials: true });
        this.$emit('changeMute');
      } else if (time === 2) {
        d.setHours(d.getHours() + 1);
        await this.$axios.$patch(`channel/${this.channelId}/${this.userId}`, { muted: true, muteEnd: d }, { withCredentials: true });
        this.$emit('changeMute');
      } else {
        d.setFullYear(d.getFullYear() + 300);
        await this.$axios.$patch(`channel/${this.channelId}/${this.userId}`, { muted: true, muteEnd: d }, { withCredentials: true });
        this.$emit('changeMute');
      }
      this.dialog = false
    }
  }
})
</script>
