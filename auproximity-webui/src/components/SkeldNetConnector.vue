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
  ImpostorBackendModel
} from '@/models/BackendModel'
import JoinModal from '@/components/JoinModal.vue'
import ClientSocketEvents from '../../../src/types/ClientSocketEvents'

@Component({
  components: { JoinModal }
})
export default class ServerConnector extends Vue {
  valid = false;
  showSnackbar = false;

  name = '';
  gameCode = this.$route.params.gamecode ? this.$route.params.gamecode.slice(0, 7) : '';

  rules = {
    required (value: string) {
      return !!value || 'Required.'
    },
    counter7 (value: string) {
      return value.length === 7 || '7 numbers'
    }
  };

  joinRoom (name: string) {
    this.name = name
    const backendModel: BackendModel = {
      gameCode: 'skeldnet',
      backendType: BackendType.Impostor
    }
    this.$emit(ClientSocketEvents.JoinRoom, {
      name,
      backendModel
    })
  }
}
</script>
<style scoped lang="stylus">
#slug-share {
  position: absolute;
  left: -9999px
}
</style>
