<template>
  <v-flex xs6>
    <v-card class="card--flex-toolbar">
      <v-toolbar card class="light-blue">
        <v-toolbar-title class="white--text">Donate Blood</v-toolbar-title>
      </v-toolbar>
      <v-list>
		  
        <v-list-tile>
          <v-list-tile-title>
            From:
          </v-list-tile-title>
          <v-list-tile-content>  
            <input v-model="coinbase" placeholder="edit me" :value=coinbase>

          </v-list-tile-content>
        </v-list-tile>
        
        <v-list-tile>
          <v-list-tile-title>
            To :
          </v-list-tile-title>
          <v-list-tile-content>  
            <v-text-field label="0" single-line v-model="addr"></v-text-field>  
          </v-list-tile-content>
        </v-list-tile>
        
        <v-list-tile>
          <v-list-tile-title>
            Unit :
          </v-list-tile-title>
          <v-list-tile-content>  
            <v-text-field label="0" single-line v-model="unit"></v-text-field>  
          </v-list-tile-content>
        </v-list-tile>
  
        <v-list-tile>
          <v-list-tile-title>
            Tokens
          </v-list-tile-title>
          <v-list-tile-content>  
            <v-text-field label="0" single-line v-model.number="token"></v-text-field>  
            {{ tokens }}
          </v-list-tile-content>
        </v-list-tile>
  
        <v-list-tile>  
          <v-spacer></v-spacer>  
          <v-list-tile-action>
            <v-btn primary dark @click.native="sendTokens">Send</v-btn>
          </v-list-tile-action>
        </v-list-tile>
  
      </v-list>
  
    </v-card>
  </v-flex>
</template>
<script>
import { CONTRACT } from '../contract'
import _ from 'lodash'

export default {
  data () {
    return {
      coinbase: 0x00,
      addr: null,
      unit: null,
      token: null,
      tokens: 0,
    }
  },
  
   mounted () {
    this.coinbase = CONTRACT._eth.coinbase
    this.getTokenBalance()
    
    CONTRACT.Transfer((err, res) => {
      this.getTokenBalance()
    });

  },

  methods: {
    sendTokens () {
      if (!web3.isAddress(this.addr)) {
        alert('Invalid address!')
        this.addr = null
        return
      }

      if (!_.isNumber(this.token) || this.token <= 0) {
        alert('Invalid token!')
        this.token = null
        return
      }
		
          console.log(this.token)
          console.log(this.addr)
          console.log(this.unit)
      CONTRACT.transfer(this.addr, web3.toWei(this.token, 'ether'), (err, res) => {
        if (!err) {
          console.log(res)
          this.addr = this.token = null
          return
        }
        console.log(err)
        this.addr = this.token = null
      })
    },    
    getTokenBalance () {
      CONTRACT.balanceOf(this.coinbase, (err, tkns) => {
        if (!err) {
          this.tokens = web3.fromWei(tkns, 'ether').toNumber()
        }
        console.log(err)
      })
    }
  }
}
</script>

