<template>
  <div class="title-wrap">
    <h1 class="mt-16 lg:mt-52 font text-black text-4xl sm:text-5xl font-light">Crypto Exchange</h1>
    <p class="my-3 font text-black text-lg sm:text-xl font-normal">Exchange fast and easy</p>
  </div>
  <TheForm />
</template>

<script>

import TheForm from "./components/TheForm.vue";

  export default {
    name: 'app',
    components: {
      TheForm
    }
  }
</script>

<style>
#app {

}
.font {
  font-family: Roboto;
}

input:active, :hover, :focus, textarea:focus {
  outline: 0;
  outline-offset: 0;
}

.svg-filter {
  filter: invert(75%) sepia(57%) saturate(6029%) hue-rotate(169deg) brightness(102%) contrast(99%);
}

</style>
