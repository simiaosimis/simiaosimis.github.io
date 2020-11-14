<template>
  <div id="app">
    <section class="hero is-fullheight">
      <div class="hero-head">
        <nav class="navbar">
          <div class="container">
            <div class="has-centered-text">
              <div>
                <a href="/">
                  <p class="title">Chess Tactics Trainer</p>
                  <p class="subtitle">Solve Chess Tactics from books.</p>
                </a>
              </div>

            </div>
            <div id="navbarMenu" class="navbar-menu">
              <div class="navbar-end"></div>
            </div>
          </div>
          <div class="has-text-right">
            Made by <a href="http://github.com/simiaosimis">@simiaosimis</a> with <a href="https://vuejs.org/">Vue.js</a>
          </div>
        </nav>
      </div>
      <div class="hero-body">
        <div class="container has-text-centered">
            <virtual-list style="height: 80%; overflow-y: auto; width: auto"
            :data-key="'uid'"
            :data-sources="this.positions"
            :data-component="tacticsPuzzle"
            />
        </div>
      </div>

    </section>
  </div>

</template>

<script>
import pgnParser from 'pgn-parser'
import { shuffle } from './Util.js'
import {defaultPositions} from './PositionData.js'
import QuestionGame from './components/QuestionGame'
import StartModal from './components/StartModal'
import TacticsPuzzle from "./components/TacticsPuzzle";
import VirtualList from 'vue-virtual-scroll-list'

export default {
  name: 'App',
  components: {
    QuestionGame,
    StartModal,
    TacticsPuzzle,
    'virtual-list': VirtualList
  },
  data () {
    return {
      currentPosition: {},
      showThreats: false,
      positionNumber: 0,
      started: false,
      isStartModalActive: true,
      positionInfo: {},
      tacticsPuzzle: TacticsPuzzle,
      positions: [],
    }
  },
  methods: {
    nextQuestion() {
      let position = this.positions[this.positionNumber]
      this.currentPosition = position
      this.positionNumber++
    },
    start(data){ //TODO improve this method
      let positions = []

      const fileUrl = '/static/MaximBlokhCHESSCombinationalMotifs.pgn' // provide file location dynamically

      fetch(fileUrl)
        .then( r => r.text() )
        .then( t => this.processData(t) )
    },
    processData(data){
      let games = pgnParser.parse(data);
      let index = 1;
      let fens = [];
      let difficulties = [];
      let whoIsToMove = [];
      games.forEach(game => {
        console.log(game);
        fens.push(game.headers[9].value);
        let dif = game.headers[0].value.substring(2);
        difficulties.push(dif)
        whoIsToMove.push(game.headers[4].value === "?" ? "Black" : "White");
        if (index % 3 === 0){
          this.positions.push({uid: index, fen: fens, difficulties: difficulties, whoIsToMove: whoIsToMove});
          console.log(whoIsToMove);
          fens = [];
          difficulties = [];
          whoIsToMove = [];
        }
        index++;
      });
    },
    genericPrompt({title, message, type}) {
      this.$modal.open({
        parent: this,
        component: StartModal,
        hasModalCard: true,
        props: {
          title: title,
          message: message,
          inputAttrs: {
            placeholder: 'e.g. hikaru',
            maxlength: 20
          },
          type: type,
          onConfirm: (data) => this.start(data),
          onCancel: () => this.start()
        }
      })
    },
    dispatch (componentName, eventName, ...rest) {
      let parent = this.$parent || this.$root
      let name = parent.$options.name

      while (parent && (!name || name !== componentName)) {
        parent = parent.$parent
        if (parent) {
          name = parent.$options.name
        }
      }

      if (parent) {
        parent.$emit.apply(parent, [eventName].concat(rest))
      }
    }
  },
  mounted(){
    this.start()
  },
  created() {
    this.positions = []
    this.$eventHub.$on('paint-threats', () => {
      this.showThreats = true
    })

    this.$eventHub.$on('next-question', () => {
      this.showThreats = false
      this.nextQuestion()
    })
    this.$eventHub.$on('start-again', () => {
      this.showThreats = false
      this.positionNumber = 0
      this.positions = shuffle(this.positions)
      this.nextQuestion()
    })
  }
}
</script>

<style>
  #app {
    margin: 1vh;
  }
  .hero.is-fullheight {
    min-height: 95vh;
  }
  .cg-board-wrap {
    margin: 0 auto;
  }
  .hero-foot {
    padding-bottom: 20px;
  }
</style>
