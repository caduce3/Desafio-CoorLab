<template>
  <div class="title">
    <div>
      <b-navbar toggleable="lg" type="dark" variant="info">
        <b-navbar-brand class="ml-2">
          <b>{{ appName }}</b>
        </b-navbar-brand>
      </b-navbar>
    </div>

    <div id='container-form' class='m-5 p-3'>
      <b-form @submit="onSubmit" @reset="onReset" v-if="show" id='base-form'>
        <h4 class="m-5">Insira o destino e o peso</h4>
        <b-form-group id="input-group-3" label="Destino" label-for="input-3" class="mb-4">
          <b-form-select
            id="input-3"
            v-model="formulario.destino"
            :options="destino"
            required
          ></b-form-select>
        </b-form-group>

        <b-form-group id="input-group-2" label="Peso" label-for="input-2" class="mb-4">
          <b-form-input
            id="input-2"
            v-model="formulario.peso"
            placeholder="Peso da carga em Kg"
            required
          ></b-form-input>
        </b-form-group>

        <b-button type="submit" variant="primary" class="m-5">Analisar</b-button>
        <!-- <b-button type="reset" variant="danger">Reset</b-button> -->

      </b-form>

      <!-- <b-card class="mt-3" header="Form Data Result">
        <pre class="m-0">{{ formulario }}</pre>
      </b-card> -->
    </div>

  </div>
  
  

</template>

<script>
import {
  BNavbar,
  BNavbarBrand,
} from 'bootstrap-vue'
import axios from 'axios';

export default {
  components: {
    BNavbar,
    BNavbarBrand,
  },
  data() {
    const appName = ''

    return {
      appName,
      formulario: {
          peso: '',
          destino: null
        },
        destino: [],
        show: true
    }
  },
  created() {
    // Implemente aqui o GET dos dados da API REST
    // para que isso ocorra na inicialização da pagina
    axios.get('http://localhost:3000/transport')
      .then(response => {
        console.log(response.data);
        const cidades = response.data.map(item => item.city);
        this.destino = cidades;
        //this.appName = response.data[0].city;
      })
      .catch(error => {
        console.error(error);
      });

      this.appName = 'Melhor Frete';

  },
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    methodFoo() {
      console.log(this.appName)
    },
    onSubmit(event) {
        event.preventDefault()
        alert(JSON.stringify(this.formulario))
      },
      onReset(event) {
        event.preventDefault()
        // Reset our formulario values
        this.formulario.peso = ''
        this.formulario.destino = null
        // Trick to reset/clear native browser formulario validation state
        this.show = false
        this.$nextTick(() => {
          this.show = true
        })
      }
  },
}
</script>

<style scoped>

@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@300&display=swap');
  #container-form {
    border: 1px solid black;
    border-radius: 15px;
    font-family: 'Oswald', sans-serif;
    font-size: 16px;
    height: 75vh;
    background-color: #00ACA6;
  }
  #base-form {
    display: flex;
    flex-direction: column;
    width: 30vw;
    height: 70vh;
    background-color: white;
    border-radius: 15px;
  }

  #base-form label {
    font-weight: bold;
    margin-bottom: 0.5em;
  }

  h4 {
    font-weight: bold;
    text-align: center;
  }

  #base-form button {
    background-color: #00ACA6;
    color: white;
  }

  #base-form button:hover {
    background-color: white;
    color: #00ACA6;
    cursor: pointer;
    border-color: #00ACA6;
  }

  #input-group-2, #input-group-3 {
    width: 80%;
    margin: auto;
   }

   #input-3 {
    width: 100%;
    height: 40px;
    border-radius: 5px;

   }

.title .navbar {
  background-color: #00aca6 !important;
}

.title .navbar-brand {
  margin-left: 20px;
}
</style>
