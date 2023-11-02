<template>
  <div class="flex flex-col h-screen max-w-md mx-auto justify-evenly">
    <div>
      <word-row v-for="(guess,i) in state.guesses" :key="i" :value="guess" :solution="state.solution" :submitted="i<state.currentGuessIndex">
        
      </word-row>
      
    </div>
    <p v-if="wonGame" class="text-center">
      Congrates
    </p>
    <p v-else-if="loseGame" class="text-center">
      Out of tries
    </p>

    <simple-keyboard @onKeyPress="handleInput" :guessedLetters="state.guessdLetters"/>
  </div>
</template>

<script setup>
  import SimpleKeyboard from './components/SimpleKeyboard.vue';
  import { reactive,onMounted,computed } from 'vue';
  import WordRow from './components/WordRow.vue';
  const state=reactive({
    solution:"books",
    guesses:["","","","",""],
    currentGuessIndex:0,
    guessdLetters:{
      miss:[],
      found:[],
      hint:[],
    }
  })
  const wonGame=computed(
    ()=>
    state.guesses[state.currentGuessIndex-1]===state.solution
  )
  const loseGame=computed(
    ()=>!wonGame.value&&state.currentGuessIndex>=6
  )
  const handleInput=(key)=>{
      if(state.currentGuessIndex>=6||wonGame.value){
        return;
      }
      const currentGuess=state.guesses[state.currentGuessIndex];
      if(key=="{enter}"){
        if(currentGuess.length==5){
          state.currentGuessIndex++;
          for(var i=0;i<currentGuess.length;i++){
            let c=currentGuess.charAt(i)
            if(c==state.solution.charAt(i)){
              state.guessdLetters.found.push(c);
            }else if(state.solution.includes(c)!=-1){
              state.guessdLetters.hint.push(c);
            }else{
              state.guessdLetters.miss.push(c);
            }
          }
        }
      }else if(key=="{bksp}"){
        state.guesses[state.currentGuessIndex]=currentGuess.slice(0,-1);
      }else if(currentGuess.length<5){
        const alphaRegex= /[a-zA-Z]/;
        if (alphaRegex.test(key)){
          state.guesses[state.currentGuessIndex]+=key
        }
      }
  }
onMounted(()=>{
  window.addEventListener("keyup",(e)=>{
    e.preventDefault();
    let key=
      e.keyCode==13
        ? "{enter}"
        : e.keyCode==8
        ?"{bksp}"
        :String.fromCharCode(e.keyCode).toLowerCase();
      handleInput(key);

  })
})

</script>

<style>
</style>
