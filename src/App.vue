<template>
  <v-app id="inspire">
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm8 md8>
            <v-card class="elevation-12">
              <v-toolbar dark color="primary">
                <v-toolbar-title>NEM-Tools</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-btn flat @click="router('V')">Verifier</v-btn>
                <v-btn flat @click="router('A')">Address</v-btn>
                <!-- <v-btn flat @click="router('V')">Link Three</v-btn> -->
              </v-toolbar>
              <v-card-text v-if="route === 'V'">
                <v-form>
                  <v-text-field prepend-icon="person" v-model="signer" name="signer" label="Signer" type="text"></v-text-field>
                  <v-text-field prepend-icon="fingerprint" v-model="signature" name="signature" label="Signature" type="text"></v-text-field>
                  <v-text-field name="data" label="Data" v-model="data" type="text" multi-line></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-text v-if="route === 'A'">
                <v-form>
                  <v-text-field prepend-icon="fingerprint" v-model="pk" name="pk" label="Private Key" type="text"></v-text-field>
                  <v-text-field prepend-icon="person" v-model="pp" name="pp" label="Public key" type="text"></v-text-field>
                  <v-text-field prepend-icon="home" name="address" label="Address" v-model="address" type="text" disabled></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions v-if="route === 'V'">
                {{error.toString()}}
                <v-spacer></v-spacer>
                <v-switch
                  :label="`JSON: ${json.toString()}`"
                  v-model="json"
                ></v-switch>
                <v-btn @click="clear">Clear</v-btn>
                <v-badge :color="color" v-model="show">
                  <span slot="badge">!</span>
                  <v-btn color="primary" @click="verify">Verify</v-btn>
                </v-badge>
              </v-card-actions>
              <v-card-actions v-if="route === 'A'">
                {{error.toString()}}
                <v-spacer></v-spacer>
                <v-radio-group v-model="networkId">
                  <v-radio
                    label="Mainnet"
                    :value="104"
                  ></v-radio>
                  <v-radio
                    label="Testnet"
                    :value="-104"
                  ></v-radio>
                  <v-radio
                    label="Mijinnet"
                    :value="96"
                  ></v-radio>
                </v-radio-group>
                <v-btn @click="clear">Clear</v-btn>
                <v-btn color="primary" @click="generate">Genrate</v-btn>
              </v-card-actions>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import nem from 'nem-sdk'

  export default {
    data () {
      return {
        route: 'V',
        pk: '',
        pp: '',
        address: '',
        networkId: 104,
        signer: '',
        signature: '',
        data: '',
        show: false,
        json: true,
        error: '',
        result: undefined
      }
    },
    props: {
      source: String
    },
    methods: {
      verify() {
        this.show = false
        this.error = ''
        if(this.json) {
          try {
            this.result = nem.crypto.verifySignature(this.signer, JSON.stringify(JSON.parse(this.data)), this.signature)
          } catch(e) {
            this.error = e;
            this.result = false;
          }
          
        } else {
          try {
            this.result = nem.crypto.verifySignature(this.signer, this.data, this.signature)
          } catch(e) {
            this.error = e;
            this.result = false;
          }
        }
        this.show = true
      },
      generate() {
        if (this.pk) {
          const keyPair = nem.crypto.keyPair.create(this.pk);
          const publicKey = keyPair.publicKey.toString();
          this.address = nem.model.address.toAddress(publicKey, this.networkId)
        } else if(this.pp) {
          this.address = nem.model.address.toAddress(this.pp, this.networkId)
        }
      },
      clear() {
        this.show = false
        this.error = ''
      },
      router(route) {
        if (route === 'V'){
          this.route = 'V'
        } else if (route === 'A') {
          this.route = 'A'
        }
      }
    },
    computed: {
      color() {
        if(this.result) {
          return 'green'
        } else {
          return 'red'
        }
      }
    }
  }
</script>