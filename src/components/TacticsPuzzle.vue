<template>
  <div class="columns is-centered is-multiline is-variable">
    <div class="column is-one-third has-background-light">
      <h3><strong>Problem {{source.uid - 2}}</strong></h3>
      {{source.whoIsToMove[0]}} To Move
      <chessboard :fen="this.positions1[this.positionNumber1]" :orientation="orientation" :showThreats="showThreats" @onMove="showInfo1"></chessboard>
      <div class="has-centered-text">
        <span v-if="source.whoIsToMove[0] === 'White'">You</span><span v-else>Level {{source.difficulties[0]}}</span> VS <span v-if="source.whoIsToMove[0] === 'Black'">You</span><span v-else>Level {{source.difficulties[0]}}</span> <br>
      </div>
      <button class="button is-link-light" @click="restartFen1"><<</button>
      <button class="button is-link-light" @click="previousFen1"><</button>
      <button class="button is-link-light" @click="nextFen1">></button>
    </div>
    <div class="column is-one-third has-background-light">
      <h3><strong>Problem {{source.uid - 1}}</strong></h3>
      {{source.whoIsToMove[1]}} To Move
      <chessboard :fen="this.positions2[this.positionNumber2]" :showThreats="showThreats" @onMove="showInfo2"></chessboard>
      <div class="has-centered-text">
        <span v-if="source.whoIsToMove[1] === 'White'">You</span><span v-else>Level {{source.difficulties[1]}}</span> VS <span v-if="source.whoIsToMove[1] === 'Black'">You</span><span v-else>Level {{source.difficulties[1]}}</span> <br>
      </div>
      <button class="button is-link-light" @click="restartFen2"><<</button>
      <button class="button is-link-light" @click="previousFen2"><</button>
      <button class="button is-link-light" @click="nextFen2">></button>
    </div>
    <div class="column is-one-third has-background-light">
      <h3><strong>Problem {{source.uid}}</strong></h3>
      {{source.whoIsToMove[2]}} To Move
      <chessboard :fen="this.positions3[this.positionNumber3]" :showThreats="showThreats" @onMove="showInfo3"></chessboard>
      <div class="has-centered-text">
        <span v-if="source.whoIsToMove[2] === 'White'">You</span><span v-else>Level {{source.difficulties[2]}}</span> VS <span v-if="source.whoIsToMove[2] === 'Black'">You</span><span v-else>Level {{source.difficulties[2]}}</span> <br>
      </div>
      <button class="button is-link-light" @click="restartFen3"><<</button>
      <button class="button is-link-light" @click="previousFen3"><</button>
      <button class="button is-link-light" @click="nextFen3">></button>
    </div>
    <hr>
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
      positions1: [],
      positionNumber1: 0,
      positions2: [],
      positionNumber2: 0,
      positions3: [],
      positionNumber3: 0,
      showThreats: false,
      started: false,
      orientation: "black",
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
    showInfo1(info){
      this.positionInfo = info

      if(this.positionInfo.history.length > 0){
        while (this.positions1.length-1 > this.positionNumber1){
          this.positions1.pop();
        }
        this.positions1.push(this.positionInfo['fen']);
        this.positionNumber1++;
      }
      this.$eventHub.$emit('game-changed', {color:this.positionInfo.turn, threats: this.positionInfo})
    },
    restartFen1(){
      if(this.positionNumber1 > 0)
        this.positionNumber1 = 0;
    },
    previousFen1(){
      if(this.positionNumber1 > 0)
      this.positionNumber1--;
    },
    nextFen1(){
      if(this.positionNumber1 < this.positions1.length - 1)
        this.positionNumber1++;
    },
    showInfo2(info){
      this.positionInfo = info

      if(this.positionInfo.history.length > 0){
        while (this.positions2.length-1 > this.positionNumber2){
          this.positions2.pop();
        }
        this.positions2.push(this.positionInfo['fen']);
        this.positionNumber2++;
      }
      this.$eventHub.$emit('game-changed', {color:this.positionInfo.turn, threats: this.positionInfo})
    },
    restartFen2(){
      if(this.positionNumber2 > 0)
        this.positionNumber2 = 0;
    },
    previousFen2(){
      if(this.positionNumber2 > 0)
        this.positionNumber2--;
    },
    nextFen2(){
      if(this.positionNumber2 < this.positions2.length - 1)
        this.positionNumber2++;
    },
    showInfo3(info){
      this.positionInfo = info

      if(this.positionInfo.history.length > 0){
        while (this.positions3.length-1 > this.positionNumber3){
          this.positions3.pop();
        }
        this.positions3.push(this.positionInfo['fen']);
        this.positionNumber3++;
      }
      this.$eventHub.$emit('game-changed', {color:this.positionInfo.turn, threats: this.positionInfo})
    },
    restartFen3(){
      if(this.positionNumber3 > 0)
        this.positionNumber3 = 0;
    },
    previousFen3(){
      if(this.positionNumber3 > 0)
        this.positionNumber3--;
    },
    nextFen3(){
      if(this.positionNumber3 < this.positions3.length - 1)
        this.positionNumber3++;
    }
  },
  created() {
    this.positions1.push(this.source.fen[0]);
    this.positions2.push(this.source.fen[1]);
    this.positions3.push(this.source.fen[2]);
  }
}
</script>

<style scoped>

</style>
