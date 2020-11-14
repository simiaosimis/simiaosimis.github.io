<template>
  <div class="columns">
    <div class="column is-4">
      <h3>Problem {{source.uid - 2}}</h3>
      {{source.whoIsToMove[0]}} To Move
      <chessboard :fen="source.fen[0]" :showThreats="showThreats" @onMove="showInfo"></chessboard>
      <div class="has-centered-text">
        <span v-if="source.whoIsToMove[0] === 'White'">You</span><span v-else>Level {{source.difficulties[0]}}</span> VS <span v-if="source.whoIsToMove[0] === 'Black'">You</span><span v-else>Level {{source.difficulties[0]}}</span> <br>
      </div>
    </div>
    <div class="column is-4">
      <h3>Problem {{source.uid - 1}}</h3>
      {{source.whoIsToMove[1]}} To Move
      <chessboard :fen="source.fen[1]" :showThreats="showThreats" @onMove="showInfo"></chessboard>
      <div class="has-centered-text">
        <span v-if="source.whoIsToMove[1] === 'White'">You</span><span v-else>Level {{source.difficulties[1]}}</span> VS <span v-if="source.whoIsToMove[1] === 'Black'">You</span><span v-else>Level {{source.difficulties[1]}}</span> <br>
      </div>
    </div>
    <div class="column is-4">
      <h3>Problem {{source.uid}}</h3>
      {{source.whoIsToMove[2]}} To Move
      <chessboard :fen="source.fen[2]" :showThreats="showThreats" @onMove="showInfo"></chessboard>
      <div class="has-centered-text">
        <span v-if="source.whoIsToMove[2] === 'White'">You</span><span v-else>Level {{source.difficulties[2]}}</span> VS <span v-if="source.whoIsToMove[2] === 'Black'">You</span><span v-else>Level {{source.difficulties[2]}}</span> <br>
      </div>
    </div>
    <br>
  </div>
</template>

<script>
import 'vue-chessboard/dist/vue-chessboard.css'
import {chessboard} from "vue-chessboard";

export default {
  name: "tactics-puzzle",
  components: {
    chessboard
  },
  data () {
    return {
      currentPosition: {},
      showThreats: false,
      positionNumber: 0,
      started: false,
    }
  },
  props: {
    index: { // index of current item
      type: Number
    },
    source: { // here is: {uid: 'unique_1', text: 'abc'}
      type: Object,
      default () {
        return {}
      }
    }
  },
  methods:{
    showInfo(info){
      this.positionInfo = info
      this.$eventHub.$emit('game-changed', {color:this.positionInfo.turn, threats: this.positionInfo})
    }
  }
}
</script>

<style scoped>

</style>
