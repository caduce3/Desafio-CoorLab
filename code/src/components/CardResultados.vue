<template>
    <div>
            <div id='h3-nenhum-dado'>
                <h3 >Nenhum dado selecionado.</h3>
            </div>
            <div class='mt-5' id='div-visible'>
                <p id="msg-melhores-opcoes">Estas são as melhores alternativas de frete que encontramos para você.</p>
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
</template>

<script>

</script>

<style scoped>
#h3-nenhum-dado{
    font-family: 'Oswald', sans-serif;
    color: white;
    width: 300px;
    position: absolute;
    top: 50%;
    left: 55%;
 }

#div-visible {
  visibility: hidden;
}

#msg-melhores-opcoes{
  font-family: 'Oswald', sans-serif;
  color: white;
  font-size: 1.5vw;
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