<template>
  <div>
    <v-card class="pa-5">
      <v-form v-model="valid" @submit.prevent="joinRoom(skeldId)">
        <v-text-field
          v-model="skeldId"
          label="skeld ID"
          :rules="[rules.required, rules.counter7]"
          counter="7"
          maxlength="7"
          outlined
        ></v-text-field>
        <v-btn
          :disabled="!valid"
          color="success"
          class="mr-4"
          type="submit"
        >
          Connect
        </v-btn>
      </v-form>
    </v-card>
    <JoinModal :game-code="skeldId" @joinroom="joinRoom($event)"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import {
  BackendModel,
  BackendType,
  ImpostorBackendModel,
  PublicLobbyBackendModel,
  PublicLobbyRegion
} from '@/models/BackendModel'
import JoinModal from '@/components/JoinModal.vue'

@Component({
  components: { JoinModal }
})
export default class ServerConnector extends Vue {
  valid = false;
  showSnackbar = false;

  name = '';
  gameCode = this.$route.params.gamecode ? this.$route.params.gamecode.slice(0, 7) : '';

  // Backends
  // eslint-disable-next-line @typescript-eslint/ban-ts-ignore
  // @ts-ignore
  backendType: BackendType = BackendType[this.$route.params.backend || 'PublicLobby'] || BackendType.PublicLobby;
  items = [
    {
      backendName: 'Official Among Us Servers',
      backendType: BackendType.PublicLobby
    },
    {
      backendName: 'Impostor Private Server',
      backendType: BackendType.Impostor
    },
    {
      backendName: 'NodePolus Private Server',
      backendType: BackendType.NodePolus
    },
    {
      backendName: 'BepInEx',
      backendType: BackendType.BepInEx
    }
  ];

  // Public Lobby Backend
  // eslint-disable-next-line @typescript-eslint/ban-ts-ignore
  // @ts-ignore
  publicLobbyRegion: PublicLobbyRegion = PublicLobbyRegion[this.$route.params.region || 'NorthAmerica'] || PublicLobbyRegion.NorthAmerica;
  regions = [
    {
      regionName: 'North America',
      regionType: PublicLobbyRegion.NorthAmerica
    },
    {
      regionName: 'Europe',
      regionType: PublicLobbyRegion.Europe
    },
    {
      regionName: 'Asia',
      regionType: PublicLobbyRegion.Asia
    }
  ];

  rules = {
    required (value: string) {
      return !!value || 'Required.'
    },
    counter7 (value: string) {
      return value.length === 7 || '7 numbers'
    },
    publicLobbyRegion (value: string) {
      return value in PublicLobbyRegion
    }
  };

  joinRoom (name: string) {
    this.name = name
    const backendModel: BackendModel = {
      gameCode: this.gameCode.toUpperCase(),
      backendType: BackendType.Impostor
    }
    if (this.backendType === BackendType.PublicLobby) {
      (backendModel as PublicLobbyBackendModel).region = this.publicLobbyRegion
    } else if (this.backendType === BackendType.Impostor || this.backendType === BackendType.NodePolus) {
      (backendModel as ImpostorBackendModel).ip = ''
    }
    this.$emit('joinroom', {
      name,
      backendModel
    })
  }

  copyShareSlug () {
    const copyText = document.getElementById('slug-share') as HTMLInputElement

    copyText.select()
    copyText.setSelectionRange(0, 99999)

    document.execCommand('copy')
    this.showSnackbar = true
  }

  get shareSlug () {
    if (this.backendType === BackendType.Impostor || this.backendType === BackendType.NodePolus) {
      return location.origin + '/' + BackendType[this.backendType] + '/' + 0 + '/' + this.gameCode
    } else if (this.backendType === BackendType.PublicLobby) {
      return location.origin + '/' + BackendType[this.backendType] + '/' + PublicLobbyRegion[this.publicLobbyRegion] + '/' + this.gameCode
    }
  }
}
</script>
<style scoped lang="stylus">
#slug-share {
  position: absolute;
  left: -9999px
}
</style>
