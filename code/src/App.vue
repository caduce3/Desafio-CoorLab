<template>
  <div id="app">
    <BestTransport/>
    <FormularioOne/>
  </div>
</template>

<script>
import BestTransport from './components/BestTransport.vue'
import FormularioOne from './components/FormularioOne.vue'

export default {
  name: 'App',
  components: {
    BestTransport,
    FormularioOne
  }
}
</script>

<style>
</style>
