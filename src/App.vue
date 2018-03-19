<template>
  <v-app id="inspire">
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm8 md4>
            <v-card class="elevation-12">
              <v-toolbar dark color="primary">
                <v-toolbar-title>NEM Signature verifier</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-tooltip bottom>
                  <v-btn
                    icon
                    large
                    :href="source"
                    target="_blank"
                    slot="activator"
                  >
                    <v-icon large>code</v-icon>
                  </v-btn>
                  <span>Source</span>
                </v-tooltip>
                <v-tooltip right>
                  <v-btn icon large href="https://github.com/QuantumMechanics/NEM-sdk#example-10" target="_blank" slot="activator">
                    <v-icon large>mdi-codepen</v-icon>
                  </v-btn>
                  <span>Codepen</span>
                </v-tooltip>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field prepend-icon="person" v-model="signer" name="signer" label="Signer" type="text"></v-text-field>
                  <v-text-field prepend-icon="fingerprint" v-model="signature" name="signature" label="Signature" type="text"></v-text-field>
                  <v-text-field name="data" label="Data" v-model="data" type="text" multi-line></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
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
      clear() {
        this.show = false
        this.error = ''
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