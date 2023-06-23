<template>
  <div class="text-center mb-40">
    <h1>Wordle</h1>
    
  </div>
  <div class="row ml-80">
    <div class="col-md-8"><Game @wordguessed="wordguess" :attemptno="attempts" /></div>
    <div class="col-md-4 text-center" style="overflow-y: scroll; height:400px;">
      <ul class="list-group w-25 p-1 guesses" v-for="tw in tw" :key="tw.key">
        <li class="list-group-item">{{tw}}</li>
      </ul>
    </div>
  
  </div>
</template>

<script>
import Game from "../components/Game"
import {ref} from 'vue'

export default {
  name: 'HomeView',
  components: {
    Game
  },

  setup() {
    let tw = ref([])
    let attempts = ref(0)

    function wordguess(value){
      //tw.value = ref(value).value

        if(tw.value.includes(value)==false)tw.value.push(value)
        attempts.value = tw.value.length
    }
    return{wordguess, tw, attempts}
  }
}
</script>

<style>
.mb-40{
  margin-bottom: 40px;
}
</style>


