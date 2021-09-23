<template>
  <form class="flex w-full mt-14 flex-col" action="">
    <div class="flex mb-3 justify-between relative lg:flex-row flex-col ">
      <TheInput
          :coins_data="coins"
          :selectedCoin="selectedLeftCoin"
          :value_data="value"
          @changeCoin="changeLeftCoin"
          @changeValue="changedValue"
      />
      <button
          type='button'
          class="flex p-1 my-3 items-center justify-end w-full flex-grow lg:justify-center lg:w-20 lg:m-0 lg:flex-grow-0"
          @click="exchangeCoin"
      >
        <svg class="w-6 h-6 lg:transform-none transform rotate-90 text-blue hover:text-blue-dark" viewBox="0 0 24 24" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
          <path d="M7.99 17H20V19H7.99V22L4 18L7.99 14V17Z" fill-rule="evenodd"/>
          <path d="M16.01 8H4V10H16.01V13L20 9L16.01 5V8Z" fill-rule="evenodd"/>
        </svg>
      </button>
      <TheInput
          :coins_data="coins"
          :selectedCoin="selectedRightCoin"
          :value_data="amount"
          @changeCoin="changeRightCoin"
      />

    </div>
    <div class="flex items-end mt-6 input-wrap flex-col lg:flex-row">
      <div class="flex flex-col w-full input-address">
        <p class="font text-base text-black font-normal">Your Ethereum address</p>
        <input type="text" class="h-12 p-4 border border-gray rounded bg-gray-light font text-base text-black font-normal">
      </div>
      <div class="relative ml-8 mt-4 lg:mt-0 w-full lg:w-72">
        <button class="rounded w-full h-12 bg-blue font font-bold uppercase text-white hover:bg-blue-dark">Exchange</button>
        <div class="error absolute top-13 w-full text-center" v-if="message">
          <p class="text-red">{{ message }}</p>
        </div>
      </div>
    </div>
  </form>
</template>

<script>

import TheInput from "./TheInput.vue";
import axios from 'axios';

export default {
  name: "form",
  components: {
    TheInput
  },
  data() {
    return {
      coins: [],
      minValue: null,
      value: null,
      amount: null,
      selectedLeftCoin: {
        "ticker": "btc",
        "name": "Bitcoin",
        "image": "https://changenow.io/images/coins/btc.svg",
        "hasExternalId": false,
        "isFiat": false,
        "featured": true,
        "isStable": false,
        "supportsFixedRate": true
      },
      selectedRightCoin: {
        "ticker": "eth",
        "name": "Ethereum",
        "image": "https://changenow.io/images/coins/eth.svg",
        "hasExternalId": false,
        "isFiat": false,
        "featured": true,
        "isStable": false,
        "supportsFixedRate": true
      },
      message: ''
    }
  },
  methods: {
    minimalExchange: function (leftCoinTicker, rightCoinTicker) {
        axios
            .get('https://api.changenow.io/v1/min-amount/'+leftCoinTicker+'_'+rightCoinTicker+'?api_key=c9155859d90d239f909d2906233816b26cd8cf5ede44702d422667672b58b0cd')
            .then(response => {
              this.minValue = response.data.minAmount;
              this.message = '';
              if (this.minValue >= this.value || this.value === '-') this.value = this.minValue
            })
            .catch((error)=>{
              this.message = "This pair is disabled now";
              this.amount = '-';
              this.value = '-';
              console.log(error)
            })
    },
    exchangeAmount: function (leftCoinTicker, rightCoinTicker, value) {
      axios
          .get('https://api.changenow.io/v1/exchange-amount/'+value+'/'+leftCoinTicker+'_'+rightCoinTicker+'?api_key=c9155859d90d239f909d2906233816b26cd8cf5ede44702d422667672b58b0cd')
          .then(response => {
            this.amount =  response.data.estimatedAmount;
          })
    },
    exchangeCoin: function () {
      let current = this.selectedLeftCoin;
      this.selectedLeftCoin = this.selectedRightCoin;
      this.selectedRightCoin = current;
    },
    changedValue: function (value) {
      this.value = value;
      this.exchangeAmount(this.selectedLeftCoin.ticker, this.selectedRightCoin.ticker, value);
    },
    changeLeftCoin: function (coin) {
      this.selectedLeftCoin = coin;
    },
    changeRightCoin: function (coin) {
      this.selectedRightCoin = coin;
    }
  },
  watch: {
    selectedLeftCoin: function () {
      this.minimalExchange(this.selectedLeftCoin.ticker, this.selectedRightCoin.ticker);
    },
    selectedRightCoin: function () {
      this.minimalExchange(this.selectedLeftCoin.ticker, this.selectedRightCoin.ticker);
    },
    minValue: function () {
      this.exchangeAmount(this.selectedLeftCoin.ticker, this.selectedRightCoin.ticker, this.value);
    }
  },
  mounted () {
    axios
        .get('https://api.changenow.io/v1/currencies?active=true&fixedRate=true')
        .then(response => { this.coins = response.data })
    this.minimalExchange(this.selectedLeftCoin.ticker, this.selectedRightCoin.ticker);

  }
}
</script>

<style scoped>

</style>