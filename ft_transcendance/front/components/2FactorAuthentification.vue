<template>
  <div>
    <v-switch
      v-model="switch2"
      color="#f28482"
      inset
      :label="`${switch1.toString()} Two Factor Authentification`"
      @change="activate2fa"
    >
      fa
    </v-switch>
    <v-card v-if="switch2" color="#f7ede2">
      <v-card-text>
        <v-img :src="qrCode" />
      </v-card-text>
    </v-card>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { authenticationStore } from '~/store'

export default Vue.extend({
  data: () => ({
    dialog: false,
    switch2: false,
    switch1: 'activate',
    user: Object(),
    qrCode: '',
  }),
  async mounted () {
    this.user = await this.$axios.$get('user/me', { withCredentials: true });
    if (this.user.isTwoFactorAuthenticationEnabled) {
      authenticationStore.setTwoFa();
      this.switch1 = 'desactivate'
    } else {
      this.switch1 = 'activate'
      authenticationStore.removeTwoFa();
    }
    this.switch2 = authenticationStore.isTwoFa;
    this.qrCode = 'data:text/plain;base64,' + await this.$axios.$post('2fa/generate', this.user, { responseType: 'arraybuffer', withCredentials: true })
      .then(response => Buffer.from(response, 'binary').toString('base64'))
  },
  methods: {
    async activate2fa () {
      if (authenticationStore.isTwoFa === true) {
        authenticationStore.removeTwoFa();
        this.switch1 = 'activate'
      } else {
        authenticationStore.setTwoFa();
        this.switch1 = 'desactivate'
      }

      await this.$axios.$patch('user', {
        isTwoFactorAuthenticationEnabled: authenticationStore.isTwoFa,
      }, { withCredentials: true });
    },
  }
})
</script>
