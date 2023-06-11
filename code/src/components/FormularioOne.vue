<template>
    <div id='container-form' class='m-5 p-3'>
        <b-form @submit="onSubmit" @reset="onReset" v-if="show" id='base-form'>
            <div style="display: flex;" id="div-img-map">
                <b-img id="img-map" :src="require('../assets/map.png')" fluid alt="Logo" style='height: 5vh; margin-top: 30px; margin-left: 40px;'></b-img>
                <h4 class="m-4" >Insira o destino e o peso</h4>
            </div>
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
                type="number"
            ></b-form-input>
            </b-form-group>

            <div class='m-5'>
            <b-button type="submit" class="mb-2">Analisar</b-button> <br>
            <b-button type="reset" id="btn-reset">Limpar</b-button>
            </div>

        </b-form>
        <CardResultados/>
        
    </div>
</template>

<script>
import axios from 'axios';
import CardResultados from './CardResultados.vue'

export default{
    components: {
        CardResultados,
    },
    data(){
        return {
            formulario: {
            peso: '',
            destino: null
        },
            destino: [],
            show: true
        }
    },
    created(){
        axios.get('http://localhost:3000/transport').then(response => {
            const cidades = response.data.map(item => item.city);
            this.destino = cidades;
        })
        .catch(error => {
            console.error(error);
        });

        this.appName = 'Melhor Frete';
    },
    methods: {
        onSubmit(event) {
            event.preventDefault()

            //PEGANDO OS VALORES DO FORMULARIO
            const destino = this.formulario.destino;
            const peso = this.formulario.peso;
            // console.log(destino, peso);

            //PEGANDO OS DADOS DA API
            axios
            .get('http://localhost:3000/transport')
            .then((response) => {
            const transportadoras = response.data;

            //FILTRANDO AS TRANSPORTADORAS PELO DESTINO INSERIDO NO FORMULÁRIO
            const freteMaisBarato = transportadoras.filter((transportadora) => transportadora.city === destino).reduce((prev, curr) => {
                
                //SE O PESO FOR MENOR OU IGUAL A 100KG, O VALOR DO FRETE SERÁ O VALOR DO FRETE LIGHT (CUSTO POR KG * PESO)
                //SE NÃO, O VALOR DO FRETE SERÁ O VALOR DO FRETE HEAVY (CUSTO POR KG * PESO)
                const custo = peso <= 100 ? curr.cost_transport_light : curr.cost_transport_heavy;

                    //PASSANDO O VALOR PARA FLOAT E TIRANDO O R$
                    const valorFrete = parseFloat(custo.replace('R$', '')) * peso;

                    //SE O VALOR DO FRETE FOR MENOR QUE O VALOR DO FRETE ANTERIOR, O VALOR DO FRETE ANTERIOR SERÁ SUBSTITUIDO PELO VALOR DO FRETE ATUAL
                    //SE NÃO, O VALOR DO FRETE ANTERIOR CONTINUARÁ SENDO O MESMO
                    if (!prev || valorFrete < prev.cost) {
                    //RETORNA O VALOR DO FRETE
                    return { ...curr, cost: valorFrete };
                    }
                return prev;
                }, null);

            const freteMaisRapido = transportadoras.filter((transportadora) => transportadora.city === destino).reduce((prev, curr) => {
                //PEGANDO O TEMPO DE ENTREGA
                const tempo = parseInt(curr.lead_time);

                //SE O PESO FOR MENOR OU IGUAL A 100KG, O VALOR DO FRETE SERÁ O VALOR DO FRETE LIGHT (CUSTO POR KG * PESO)
                //SE NÃO, O VALOR DO FRETE SERÁ O VALOR DO FRETE HEAVY (CUSTO POR KG * PESO)
                if (!prev || tempo < prev.time) {
                    const custo = peso <= 100 ? curr.cost_transport_light : curr.cost_transport_heavy;
                    const valorFrete = parseFloat(custo.replace('R$', '')) * peso;

                    //RETORNA O TEMPO DE ENTREGA E O VALOR DO FRETE
                    return { ...curr, time: tempo, valorFrete };
                }

                return prev;
                }, null);

            
            this.melhorFrete = {
                maisBarato: freteMaisBarato,
                maisRapido: freteMaisRapido,
            };

            // console.log('Frete mais barato:', this.melhorFrete.maisBarato);
            // console.log('Frete mais rápido:', this.melhorFrete.maisRapido);

            //PEGANDO OS ELEMENTOS DO HTML PARA INSERIR OS DADOS
            const nomeTransportadoraFreteMenorValor = document.getElementById('nomeTransportadoraFreteMenorValor');
            const tempoEstimadoTransportadoraFreteMenorValor = document.getElementById('tempoEstimadoTransportadoraFreteMenorValor');
            const resultadoFreteMenorValor = document.getElementById('resultadoFreteMenorValor');
            const nomeTransportadoraFreteMaisRapido = document.getElementById('nomeTransportadoraFreteMaisRapido');
            const tempoEstimadoTransportadoraFreteMaisRapido = document.getElementById('tempoEstimadoTransportadoraFreteMaisRapido');
            const resultadoFreteMaisRapido = document.getElementById('resultadoFreteMaisRapido');

            //INSERINDO OS DADOS NO HTML
            nomeTransportadoraFreteMenorValor.innerHTML = this.melhorFrete.maisBarato.name;
            tempoEstimadoTransportadoraFreteMenorValor.innerHTML = this.melhorFrete.maisBarato.lead_time;
            resultadoFreteMenorValor.innerHTML = this.melhorFrete.maisBarato.cost.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
            nomeTransportadoraFreteMaisRapido.innerHTML = this.melhorFrete.maisRapido.name;
            tempoEstimadoTransportadoraFreteMaisRapido.innerHTML = this.melhorFrete.maisRapido.lead_time;
            resultadoFreteMaisRapido.innerHTML = this.melhorFrete.maisRapido.valorFrete.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });

            //CARD APARECER APÓS PREENCHER O FORMULÁRIO
            const containerCardsResultado = document.getElementById('div-visible');
            containerCardsResultado.style.visibility = 'visible';

            //NEHUM DADO ENCONTRADO DESAPARECER APÓS PREENCHER O FORMULÁRIO
            const h3NenhumDado = document.getElementById('h3-nenhum-dado');
            h3NenhumDado.style.visibility = 'hidden';

            const btnReset = document.getElementById('btn-reset');
            btnReset.style.visibility = 'visible';
            
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

            //CARD DESAPARECER APÓS RESETAR
            const containerCardsResultado = document.getElementById('div-visible');
            containerCardsResultado.style.visibility = 'hidden';

            //NEHUM DADO ENCONTRADO APARECER APÓS RESETAR
            const h3NenhumDado = document.getElementById('h3-nenhum-dado');
            h3NenhumDado.style.visibility = 'visible';
            

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

#input-group-2, #input-group-3 {
    width: 80%;
    margin: auto;
}

#input-3 {
    width: 100%;
    height: 40px;
    border-radius: 5px;

}

#btn-reset{
  visibility: hidden;
}

#btn-reset {
  background-color: black;
}

#btn-reset:hover {
  background-color: red;
  color: white;
}

button {
  background-color: #00ACA6;
  color: white;
  width: 100%;
  border-color: #00ACA6; 
}

button:hover {
  background-color: white;
  color: #00ACA6;
  cursor: pointer;
  border-color: #00ACA6;
}

h4 {
  font-weight: bold;
  text-align: center;
  font-size: 2vw;
}

@media (max-width: 940px) {
  
  #div-img-map{
    flex-direction: column;
    align-items: center;
  }
  #img-map{
    width: 30px;
    height: 30px;
    margin-right: 40px;
  }
}

</style>