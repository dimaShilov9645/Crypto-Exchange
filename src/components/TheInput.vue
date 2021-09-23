<template>
  <div class="input-search flex flex-grow relative" v-if="isSearch">
    <input
        type="text"
        placeholder="Search"
        class="h-12 p-4 flex-grow border border-r-0 border-gray rounded rounded-tr-none rounded-br-none bg-gray-light font text-base text-black font-normal"
        v-model="searchCoinsValue"
    >
    <div class="flex items-center w-36 justify-end border border-l-0 border-gray rounded rounded-tl-none bg-gray-light rounded-bl-none">
      <button @click="isSearch=false">
        <svg xmlns="http://www.w3.org/2000/svg" class="w-4 m-2 text-blue-light" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </button>
    </div>
    <div class="flex absolute w-full top-12 flex-col border border-t-0 border-gray rounded rounded-tr-none rounded-tl-none overscroll-behavior: auto overflow-auto h-56">
      <label
          class="flex font text-base text-black font-normal p-1.5 bg-gray-light hover:bg-white-light cursor-pointer"
          v-for="coin in searchCoins"
          :key="coin.index"
          @click="isSearch=false; $emit('changeCoin', coin)"
      >
        <img :src="coin.image.replace('/sprite/currencies/', '/coins/')" class="flex items-center justify-center w-5 m-1.5 svg-filter" alt="">
        <h2 class="m-1.5">{{ coin.ticker.toUpperCase() }}</h2>
        <p class="m-1.5 text-blue-light">{{coin.name}}</p>
      </label>
    </div>
  </div>

  <div class="input-text flex flex-grow relative" v-else >
    <input
        class="h-12 p-4 flex-grow border border-r-0 border-gray rounded rounded-tr-none rounded-br-none bg-gray-light font text-base text-black font-normal"
        type="text"
        v-model="changeValue"
    >
    <label
        class="flex w-36 items-center border border-l-0 border-gray rounded rounded-tl-none rounded-bl-none bg-gray-light justify-between label cursor-pointer"
        @click="isSearch=true"
    >
      <div class="transform translate-x-2">
        <img
            :src="select.image.replace('/sprite/currencies/', '/coins/')"
            class="flex items-center justify-center w-5 transform rotate-12 svg-filter" alt="">
      </div>
      <p class="font text-base text-black font-normal">{{ select.ticker.toUpperCase() }}</p>
      <svg class="w-5 m-1 text-blue-light" viewBox="0 0 20 20" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
        <path fill-rule="evenodd" d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z" clip-rule="evenodd" />
      </svg>
    </label>
  </div>
</template>

<script>


export default {
  name: "TheInput",
  props: {
    coins_data: {
      type: Array,
      default: () => []
    },
    selectedCoin: {
      type: Object,
      default: () => {}
    },
    value_data: {
      type: Number,
      default: null
    }
  },
  data () {
    return {
      isSearch: false,
      searchCoinsValue: '',

      filteredCoins: [],
    }
  },
  computed: {
    changeValue: {
      get() {
        return this.value_data
      },
      set(value) {
        this.$emit('changeValue', value)
      }

    },
    searchCoins() {
      if(this.searchCoinsValue === '') {
        return this.coins_data
      }
      else return this.filteredCoins
    },
    select() {
      return this.selectedCoin
    },
  },
  methods: {
    coinsSearch: function (value) {
      if (value.length) {
        this.filteredCoins = this.coins_data.filter(function(item) {
          return item.name.toLowerCase().includes(value.toLowerCase());
        });
      }
    },
  },
  watch: {
    searchCoinsValue: function () {
      this.coinsSearch(this.searchCoinsValue);
    },
  },

}
</script>

<style scoped>

.label:before {
  content: " ";
  height  : 60%;
  border-right: 1px solid #E3EBEF;
}

</style>