<template>
  <v-layout row wrap justify-center id="wrapper">
    <v-flex xs12 md4 offset-md1 class="text-xs-center centered">
      <img id="logo" class="logo" src="~@/assets/logo.png" alt="electron-vue">
    </v-flex>
    <v-flex xs12 md6 class="text-xs-center centered">
      <img id="logo" class="logo" src="/static/v.png" alt="Vuetifyjs">
    </v-flex>
    <v-flex xs10 class="mt-3">
      <v-card>
        <v-card-text>
          <p>MuseJsElectron</p>
          <p>Let the brains begin!</p>
          <div class="text-xs-right">
            <em><small>&mdash; </small></em>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn primary flat router to="/inspire">Continue</v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
    <v-flex xs10 class="mt-4">
      <system-information></system-information>
    </v-flex>
    <v-flex xs10 class="mt-4">
      <v-card>
        <v-card-title class="headline">Docs</v-card-title>
        <v-divider></v-divider>
        <v-card-actions class="pt-3 pb-3">
          <v-spacer></v-spacer>
          <v-btn class="link-btn" @click="connectMuse()">Connect Muse</v-btn>
          <v-spacer></v-spacer>
          <v-btn class="link-btn" @click="readEEG()">Read EEG</v-btn>
          <v-spacer></v-spacer>
          <v-btn class="link-btn" @click="open('https://www.nature.com/subjects/electroencephalography-eeg')">About EEG</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
  import SystemInformation from './WelcomeView/SystemInformation'
  import { MuseClient, MUSE_SERVICE } from 'muse-js'

  export default {
    name: 'welcome',
    data () {
      return {
        client: null
      }
    },
    components: {
      SystemInformation,
      MuseClient
    },
    mounted () {
      if (!navigator.bluetooth) {
        console.log('Web Bluetooth API is not available')
      } else {
        console.log('Bluetooth Available!')
      }
    },
    methods: {
      open (link) {
        this.$electron.shell.openExternal(link)
      },
      connectMuse () {
        // const noble = require('noble')
        const bluetooth = require('bleat').webbluetooth
        console.log(MUSE_SERVICE)
        async function connect () {
          console.log('Trying to connect...')
          let device = await bluetooth.requestDevice({
            filters: [{services: [MUSE_SERVICE]}]
          })
          const gatt = await device.gatt.connect()
          let client = new MuseClient()
          await client.connect(gatt)
          await client.start()
          console.log('Connected muse!')
          this.client = client
        }
        connect()
      },
      readEEG () {
        if (this.client) {
          this.client.eegReadings.subscribe(reading => {
            console.log(reading)
          })
        }
      },
      readTelemetry () {
        if (this.client) {
          this.client.telemetryData.subscribe(telemetry => {
            console.log(telemetry)
          })
        }
      },
      readAccelerometer () {
        if (this.client) {
          this.client.accelerometerData.subscribe(accel => {
            console.log(accel)
          })
        }
      }
    }
  }
</script>

<style scoped>
  .centered
  {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .logo
  {
    max-width: 100%;
  }

  .link-btn
  {
    width: 150px;
  }
</style>
