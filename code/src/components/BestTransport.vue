<template>
  <div class="title">
    <div>
      <b-navbar toggleable="lg" type="dark" variant="info">
        <b-navbar-brand class="ml-2">        
          <b-img :src="require('../assets/logo.png')" fluid alt="Logo" style='width: 50px; margin-right: 20px; margin-left: 20px;'></b-img>
          <b>{{ appName }}</b> 
        </b-navbar-brand>
      </b-navbar>
    </div>

    <FormularioOne/>

  </div> 
</template>

<script>
import {
  BNavbar,
  BNavbarBrand,
} from 'bootstrap-vue'

import FormularioOne from './FormularioOne.vue'

export default {
  components: {
    BNavbar,
    BNavbarBrand,
    FormularioOne
  },
  data() {
    const appName = ''

    return {
      appName,
    }
  },
  created() {
    // Implemente aqui o GET dos dados da API REST
    // para que isso ocorra na inicialização da pagina
    this.appName = 'Melhor Frete';
  },
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    methodFoo() {
      
    },
  },
}
</script>

<style scoped>

.title .navbar {
  background-color: #00aca6 !important;
}

.title .navbar-brand {
  margin-left: 20px;
}

@media (max-width: 940px) {
  #container-form {
    flex-direction: column;
    height: 900px;
    align-items: center;
  }

}

</style>
