<template>
  <v-container>
    <v-row>
      <v-col cols="12" md="6">
        <SkeldNetConnector @joinroom="joinRoom($event)"/>
      </v-col>
      <v-col cols="12" md="6">
        <ServerDisplayer />
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import { BackendModel } from '@/models/BackendModel'
import ClientSocketEvents from '@/models/ClientSocketEvents'
import SkeldNetConnector from '@/components/SkeldNetConnector.vue'
import ServerDisplayer from '@/components/ServerDisplayer.vue'
import consts from '@/consts'

@Component({
  components: {
    SkeldNetConnector,
    ServerDisplayer
  }
})
export default class Home extends Vue {
  discordUrl = consts.DISCORD_INVITE_URL
  githubUrl = consts.GITHUB_URL

  joinRoom (event: { name: string; backendModel: BackendModel }) {
    const payload = {
      name: event.name,
      backendModel: event.backendModel
    }
    this.$store.commit('setJoinedRoom', true)
    this.$store.commit('setNameAndBackendModel', payload)
    this.$socket.client.emit(ClientSocketEvents.JoinSkeldNetRoom, payload)
  }
}
</script>
