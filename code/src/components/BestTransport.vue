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

        <div class='m-5'>
          <b-button type="submit" class="mb-2">Analisar</b-button> <br>
          <b-button type="reset" id="btn-reset">Reset</b-button>
        </div>

      </b-form>

      <!-- <b-card class="mt-3" header="Form Data Result">
        <pre class="m-0">{{ formulario }}</pre>
      </b-card> -->

      <div class='mt-5'>
        <div class="container-cards-resultado mb-3 mt-5">
          <div class='container-cards-resultado-1'>
            <p>
              <strong>Frete com o menor valor</strong> <br>
              Transportadora: <span id='nomeTransportadoraFreteMenorValor'></span><br>
              Tempo estimado: <span id='tempoEstimadoTransportadoraFreteMenorValor'></span>
            </p>
          </div>
          <div class='container-cards-resultado-2'>
            <p>
              <strong>Preço</strong> <br>
              <span id='resultadoFreteMenorValor'></span>
            </p>
          </div>
        </div>
        <div class="container-cards-resultado">
          <div class='container-cards-resultado-1'>
            <p>
              <strong>Frete mais rápido</strong> <br>
              Transportadora: <span id='nomeTransportadoraFreteMaisRapido'></span><br>
              Tempo estimado: <span id='tempoEstimadoTransportadoraFreteMaisRapido'></span>
            </p>
          </div>
          <div class='container-cards-resultado-2'>
            <p>
              <strong>Preço</strong> <br>
              <span id='resultadoFreteMaisRapido'></span>
            </p>
          </div>
        </div>
      </div>

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

        const destino = this.formulario.destino;
        const peso = this.formulario.peso;
        console.log(destino, peso);

        axios
          .get('http://localhost:3000/transport')
          .then((response) => {
          const transportadoras = response.data;

          const freteMaisBarato = transportadoras
            .filter((transportadora) => transportadora.city === destino)
            .reduce((prev, curr) => {
              const custo =
                peso <= 100 ? curr.cost_transport_light : curr.cost_transport_heavy;

              if (!prev || custo < prev.cost) {
                return { ...curr, cost: custo };
              }

              return prev;
            }, null);

          const freteMaisRapido = transportadoras
            .filter((transportadora) => transportadora.city === destino)
            .reduce((prev, curr) => {
              const tempo = parseInt(curr.lead_time);

              if (!prev || tempo < prev.time) {
                return { ...curr, time: tempo };
              }

              return prev;
            }, null);

          
          this.melhorFrete = {
            maisBarato: freteMaisBarato,
            maisRapido: freteMaisRapido,
          };

          console.log('Frete mais barato:', this.melhorFrete.maisBarato);
          console.log('Frete mais rápido:', this.melhorFrete.maisRapido);
    })
    .catch((error) => {
      console.error(error);
       
          });
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

@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@300&display=swap');
  #container-form {
    border: 1px solid black;
    border-radius: 15px;
    font-family: 'Oswald', sans-serif;
    font-size: 16px;
    height: 75vh;
    background-color: #00ACA6;
    display: flex;
  }
  #base-form {
    display: flex;
    flex-direction: column;
    width: 30vw;
    height: 70vh;
    background-color: white;
    border-radius: 15px;
    margin-right: 5%;
  }

  #base-form label {
    font-weight: bold;
    margin-bottom: 0.5em;
  }

  h4 {
    font-weight: bold;
    text-align: center;
  }

  button {
    background-color: #00ACA6;
    color: white;
    width: 100%;
    border-color: #00ACA6;
    
  }

  #btn-reset {
    background-color: black;
  }

  #btn-reset:hover {
    background-color: red;
    color: white;
  }

  button:hover {
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


   .container-cards-resultado{
      display: flex;
      flex-direction: row; 
      font-size: 1.3vw;  
      text-align: left;
   }

   .container-cards-resultado-1{
      margin-right: 20px;
      border-radius: 5px;
      border: 1px solid black;
      padding: 20px;
      width: 30vw;
      height: 20vh;
      background-color: white;
   }
   .container-cards-resultado-2{
      margin-right: 20px;
      border-radius: 5px;
      border: 1px solid black;
      padding: 20px;
      width: 10vw;
      height: 20vh;
      background-color: white;
   }
</style>
