<template>
  <div id="app">
    <div class="urna">
      <Tela 
        :tela="tela"
        :numeroVoto="numeroVoto"
        :quantidadeNumeros="quantidadeNumeros"
        :candidato="candidato"
      />

      <Teclado
        :adicionarNumero="adicionarNumero" 
        :corrigir="corrigir"
        :confirmar="confirmar"
        :votarEmBranco = "votarEmBranco"
      />

    </div>

  </div>
</template>

<script>
import '@/css/global.css';

import Teclado from '@/components/Teclado';
import Tela from '@/components/Tela';

import confirmAudio from '@/assets/audios/confirm.wav'
import keyAudio from '@/assets/audios/key.wav'

export default {
  name: 'App',
  components: {
    Teclado,
    Tela
  },
  methods: {
    adicionarNumero(numero) {
      //som da tecla
      this.executarSom(keyAudio);
      //limite de numeros
      if(this.numeroVoto.length == this.quantidadeNumeros) {
        return false
      }
      //adicionar numero selecionado
      this.numeroVoto += ''+numero;

      // Verifica o candidato escolhido
      this.verificarCandidato();
    },
    verificarCandidato() {
      // voto incompleto
      if(this.numeroVoto.length < this.quantidadeNumeros) {
        return false;
      }

      //verifica se existe o candidato
      if(this.candidatos[this.tela][this.numeroVoto]) {
        this.candidato = this.candidatos[this.tela][this.numeroVoto];
        return true;
      }

      //voto nulo
      this.candidato = {
        nome: 'Voto nulo',
        partido: 'Voto nulo',
        imagem: ''
      }
    },
    corrigir() {
      //som da tecla
      this.executarSom(keyAudio);
      this.limpar();
    },
    limpar() {
      this.candidato = {}
      this.numeroVoto = ''
    }, 
    confirmar() {
      if(this.numeroVoto.length < this.quantidadeNumeros) {
        return false;
      }

      return this.avancarTela();
    },
    avancarTela() {
      //som de confirmação
      this.executarSom(confirmAudio);

      if(this.tela == 'presidente') {
        this.tela = 'vereador';
        this.quantidadeNumeros = 5;
        return this.limpar();
      }
      this.tela = 'fim';

      var instancia = this;

      setTimeout(function() {
        instancia.tela = 'presidente';
        instancia.quantidadeNumeros = 2;
        return instancia.limpar();

      }, 5000);
    },
    votarEmBranco() {
      if(this.tela == 'fim') return false;

      this.limpar();
      this.avancarTela();
    },
    executarSom(arquivoSom) {
      if(arquivoSom) {
        let audio = new Audio(arquivoSom);
        audio.play();
      }
    }
  },
  data() {
    return {
      tela: 'presidente',
      numeroVoto: '',
      quantidadeNumeros: 2,
      candidato: {},
      candidatos: {
        "presidente":{
          "13":{
            "nome": "Goku",
            "partido": "Dragon Ball",
            "imagem": "https://sm.ign.com/t/ign_br/screenshot/default/goku_an6e.h720.jpg"
          },
          "45":{
            "nome": "Mun-ra",
            "partido": "Thundercats",
            "imagem": "https://pbs.twimg.com/media/DmWO4pJXgAIRynF.jpg"
          }
        },
        "vereador":{
          "25000":{
            "nome": "Esqueleto",
            "partido": "He-man",
            "imagem": "https://universoreverso.com.br/wp-content/uploads/2020/02/esqueleto-he-man.jpg"
          },
          "12345":{
            "nome": "Vegeta",
            "partido": "Dragon Ball",
            "imagem": "http://pm1.narvii.com/6824/2a823bd4cefce6cb0e39a5265b33cdf8583baf83v2_00.jpg"
          },
          "54321":{
            "nome": "Gorpo",
            "partido": "He-man",
            "imagem": "https://www.purarteadesivos.com.br/wp-content/uploads/2021/04/adesivo-personalizado-gorpo-he-man-sticker-recorte-eletronico-anos-80-90-geek-pura-arte-adesivos.png"
          }
        }
      }
    }
  }
}
</script>

<style>
#app {
  background-color: var(--background-color);
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.urna {
  width: 1000px;
  height: 500px;
  background-color: var(--ballot-box-background-color);
  padding: 30px;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
}
</style>
