

<script setup>
import { computed, initCustomFormatter, ref } from 'vue';
import {game} from '../../composable/game.js'
import '../../assets/style.css';
import Header from "./components/Header.vue";
import Figure from "./components/Figure.vue";
import Word from "./components/Word.vue";
import Pop from "./components/Popup.vue";
import keyboard from "./components/KEYBOARD/keyboard.vue"
import hint from "./components/hint.vue"
import { playsound } from '../../composable/sound.js';

const props = defineProps({
     question: { type:Array,  default:[]} ,
     change: { type:Boolean,  default:false}, 
     name: { type:String,  default:"Hangman"}
})

let count=ref(0)
const allWord =  ref(props.question)


let noQuestion =  ref(0)
const clickKd =(event)=>{
           const letter = event.toLowerCase()
         guessedLetters.value.push(letter);        





}
const emits = defineEmits(["ending","chang"])
let mygame=ref(new game(allWord.value))
let mygamelist=ref(mygame.value.createdgame())
const word = ref(allWord.value[mygamelist.value[0]].word);
 const getmean = computed(() =>
 allWord.value.find((x)=>x.word===word.value).meaning
);
//  const meanings = ref(getMeaning());
const guessedLetters = ref([]);
const letters = computed(() => word.value.split(""));
const wrongLetters = computed(() =>
    guessedLetters.value
        .filter((l) => !letters.value.includes(l))
        .reduce((x, y) => (x.includes(y) ? x : [...x, y]), [])
);
const correctLetters = computed(() =>
    guessedLetters.value.filter((l) => letters.value.includes(l))
);
const status = computed(()=>
{
    if(wrongLetters.value.length === 6) {
    playsoundS("losegame")
    return 'lost'

    }

    if(letters.value.every(l => correctLetters.value.includes(l))){
        ++noQuestion.value
        
        playsoundS()
        return 'win'
    }
    
    else return ''
})
const reset = () => {
    guessedLetters.value =[]
    count.value=0
    mygamelist.value=mygame.value.createdgame()
    word.value = allWord.value[mygamelist.value[0]].word
    resetkb.value++
    endgame.value=0
    noQuestion.value=0  
    playsound()
}

const next = () => {
    playsound()
    if(count.value<mygamelist.value.length-2){
        guessedLetters.value =[]
    word.value = allWord.value[mygamelist.value[++count.value]].word
    resetkb.value++
    }
    else if(count.value<mygamelist.value.length-1){
    guessedLetters.value =[]
    word.value = allWord.value[mygamelist.value[++count.value]].word
    resetkb.value++
    endgame.value++
    }
}

let resetkb=ref(0)
let endgame=ref(0)

const closegame=()=>{
    emits('ending')
    reset()
    playsound()
}

const initGame=()=>{
   if(props.change==true) {
    let kk=props.question
    allWord.value=kk
    mygame.value=new game(allWord.value)
    mygamelist.value=mygame.value.createdgame()
    reset()
    emits('chang')
   }
}

const losegame=ref('')
const winround=ref('')
function playsoundS(para){
    if(para=="losegame"){
        losegame.value.play()
    }else{
        winround.value.play()
    }
}
</script>

<template>
    <p class="hidden">{{ initGame() }}</p>



    <audio   src="losegame.mp3"   ref="losegame" type="audio/mpeg" ></audio>
    <audio   src="winrounds.mp3"   ref="winround" type="audio/mpeg" ></audio>
    <button @click="closegame()" class="text-blue-300 border-2 bg-white rounded-lg  px-1 py-1 my-2 mx-2 hover:bg-slate-900 hover:text-white hover:border-black hover:cursor-pointer transition duration-150  active:scale-90">Back To Select Category</button>

    <div class="-mt-11">
        <Header  class="mb-3" :wrongcount="wrongLetters.length" :name="props.name"  :CountWord="noQuestion" :AllWordNo="mygamelist.length"/>
        <div ><hint :hint="getmean" :wrongcount="wrongLetters.length"/></div>
        <div class="grid justify-center items-center">
        <Figure :wrongcount="wrongLetters.length"/>
        </div>
        <div class="flex justify-center items-center" :class="[status=='win'?'hidden':'',status=='lost'?'hidden':'']">
        <Word :letters="letters" :correct-letters="correctLetters"  />
        </div>
        <div :class="[status=='win'?'hidden':'',status=='lost'?'hidden':'']">
        <keyboard  @press="clickKd($event)" :wordques="word" :statuscode="resetkb"  />      
    </div>               
    <Pop :status="status " :word="word" @reset ="reset()" @next="next()" :endgame="endgame" @menu="closegame()"/>
    </div>
</template>
<style scoped>
</style>

>>>>>>> b5e3f4b246f316f95d399110edbfe348a6c9aa1c
