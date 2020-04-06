<template>
  <div class="holder">
    <div class="container">
      <img class="icon" src="/images/DebriefLogo_white.svg" alt="Debrief logo" height="50">
      <h1>GENERATE NEW WALLET</h1>

      <p>
        Enter your Debrief wallet to recieve a new linked ETH wallet
      </p>

      <div class="">
        <div class="form-group">
          <label for="email" class="form-label">Wallet Address *</label>
          <input formnovalidate="" id="wallet" class="form-control " type="text" placeholder="0x" value="" v-model="wallet" @input="inputChange">
          <div v-if="errors.wallet" class="invalid-feedback">Please enter a valid wallet address</div>
        </div>

        <button class="submit-button" ref="button" id="button" @click="submit" :disabled="errors.wallet || !wallet">Send</button>
      </div>

      <div v-if="ethWallet">
        <h2>Your new ETH wallet address is:</h2>
        <h3 class="wallet">{{ethWallet}}</h3>
      </div>
      <canvas :v-cloak="`${!ethWallet || errors.wallet}`" class="qrcode" ref="canvas"></canvas>
    </div>
  </div>
</template>

<script>
import './assets/scss/main.scss'
const config = require('./config.json')
const QRCode = require('qrcode')
const axios = require('axios')

export default {
  name: 'app',
  data() {
    return {
      wallet: null,
      ethWallet: null,
      errors: {
        wallet: false
      }
    }
  },
  components: {

  },
  methods: {
    submit() {
      if(this.ethWallet) return

      const this_var = this

      axios.post(config.api, {
        "dbf_net_address": this.wallet
      }).then((res) => {
        this_var.ethWallet = res.data.EthNetWallet
        this_var.generateQRCode(this_var.ethWallet)
      }).catch((error) => {
        console.error(error)
      })
    },
    inputChange(e) {
      if(!(/^(0x){1}[0-9a-fA-F]{40}$/i.test(this.wallet))) {
        this.errors.wallet = true
      } else {
        if(this.wallet && this.wallet !== '') {
          this.errors.wallet = false
        }
      }
    },
    generateQRCode(val) {
      QRCode.toCanvas(this.$refs.canvas, val, { errorCorrectionLevel: 'H', width: 350 }, (err) => {
        if(err) console.error(err)
      })
    },
  },
  mounted() {

  }
}
</script>

<style lang="scss">

</style>
