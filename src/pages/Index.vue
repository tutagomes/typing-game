<template>
  <q-page class style="font-size: 2em">
    <div>
      <div v-if="mostraTimer">Tempo: {{timer}}</div>
      <div v-if="mostraTimer || pontos > 0">Pontos: {{pontos}}</div>
      <br>
      <q-btn color="primary" label="Iniciar" v-if="mostraBotao" @click="inicioJogo()"/>
            <q-circular-progress
      show-value
      class="q-ma-md"
      :value="value"
      size="600px"
      :thickness="0.25"
      color="primary"
      track-color="grey-3"
    >
      <q-scroll-area ref='scroll' style="height: 380px; padding: 30px; " class="full-width justify-center">
        <div class="no-wrap justify-center" style="text-align: center; padding-bottom: 400px;font-size: 0.3em !important">
        <div v-for="(plv, index) in listaPalavras" :key="index" style='margin: 0px 10px 0px 10px; '>
          <div v-if="palavraAtual < index" >
            <a :style="{textDecoration: plv.decoration}">{{plv.palavra}}</a>
          </div>
          <div v-if="palavraAtual === index" style='display: inline-flex;; border-bottom: 5px solid #027BE3'>
            <a v-for="(letra, idx) in palavra" :key="idx" :style="{ color: letra.color, background: letra.back }">
              {{letra.letra}}
            </a>
          </div>
          <div v-if="palavraAtual > index">
            <a :style="{textDecoration: plv.decoration}">{{plv.palavra}}</a>
          </div>
        </div>
        </div>
      </q-scroll-area>
    </q-circular-progress>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import { clearInterval } from "timers"
const list = [
  "ACCOUNT",
  "ACCURATE",
  "ACRES",
  "ACROSS",
  "ACT",
  "ACTION",
  "ACTIVE",
  "ACTIVITY",
  "ACTUAL",
  "ACTUALLY",
  "ADD",
  "ADDITION",
  "ADDITIONAL",
  "ADJECTIVE",
  "ADULT",
  "ADVENTURE",
  "ADVICE",
  "AFFECT",
  "AFRAID",
  "AFTER",
  "AFTERNOON",
  "AGAIN",
  "AGAINST",
  "AGE",
  "AGO",
  "AGREE"
]
export default {
  name: "PageIndex",
  data() {
    return {
      pontos: 0,
      listaPalavras: [],
      palavra: [],
      caracterAtual: 0,
      palavraAtual: -1,
      scroll: 0,
      mostraBotao: true,
      timer: "",
      mostraTimer: false,
      intervalo: null,
      value: 100
    }
  },
  created() {
    document.addEventListener("keydown", this.typing, false)
  },
  methods: {
    inicioJogo() {
      this.$refs.scroll.setScrollPosition(0)
      this.listaPalavras = []
      for (var i = 0; i < list.length; i++) {
        let plv = {}
        plv.palavra = list[i]
        plv.decoration = ""
        plv.color = "white"
        this.listaPalavras.push(plv)
      }
      this.shuffleArray(this.listaPalavras)
      this.pegaPalavraNova()
      this.pontos = 0
      this.mostraBotao = false
      this.mostraTimer = true
      this.startTimer(30)
    },
    shuffleArray(array) {
      for (var i = array.length - 1; i > 0; i--) {
        var j = Math.floor(Math.random() * (i + 1))
        var temp = array[i]
        array[i] = array[j]
        array[j] = temp
      }
    },
    pegaPalavraNova() {
      if (this.palavraAtual > -1) {
        this.listaPalavras[this.palavraAtual].decoration = "line-through"
        this.$refs.scroll.setScrollPosition(this.palavraAtual*46)
      }
      this.palavraAtual++

      let plv = this.listaPalavras[this.palavraAtual].palavra
      this.palavra = []
      console.log(plv)
      this.caracterAtual = 0
      for (var i = 0; i < plv.length; i++) {
        let letra = {}
        letra.color = "black"
        letra.back = "white"
        letra.letra = plv[i]
        this.palavra.push(letra)
      }
    },
    startTimer(duration) {
      var timer = duration,
        minutes,
        seconds
      let self = this
      if (this.intervalo) {
        window.clearInterval(this.intervalo)
      }
      this.intervalo = setInterval(function() {
        minutes = parseInt(timer / 60, 10)
        seconds = parseInt(timer % 60, 10)
        minutes = minutes < 10 ? "0" + minutes : minutes
        seconds = seconds < 10 ? "0" + seconds : seconds

        self.timer = minutes + ":" + seconds
        self.value = (timer/duration)*100
        if (--timer < 0) {
          self.mostraBotao = true
          self.mostraTimer = false
          self.palavra = []
          window.clearInterval(self.intervalo)
        }
      }, 1000)
    },
    typing(e) {
      let self = this
      let typed = String.fromCharCode(e.which)
      if (e.keyCode < 50) {
        return
      }
      // console.log(self.palavra[self.caracterAtual])
      if(!self.palavra[self.caracterAtual]) { 
        return
      } 
      if (typed !== self.palavra[self.caracterAtual].letra && self.caracterAtual === 0) {
        return
      }
      if (typed === self.palavra[self.caracterAtual].letra ) {
        self.palavra[self.caracterAtual].color = "white"
        self.palavra[self.caracterAtual].back = "#027BE3"
        self.caracterAtual++
        if (self.caracterAtual >= self.palavra.length) {
          self.pontos += self.palavra.length
          self.pegaPalavraNova()
        }
      } else if (self.palavra[self.caracterAtual]) {
        self.pegaPalavraNova()
      }
    }
  }
}
</script>
