<template>
  <div id="app">
    <flash-card :question="question" :answer="answer" @refresh="refresh"></flash-card>
    <new-question :class="{'open': addQuestionOpen}" @addSuccess="addQuestionOpen = false"></new-question>
  </div>
</template>

<script>
import FlashCard from './components/FlashCard.vue'
import NewQuestion from './components/NewQuestion.vue'

function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}

export default {
  name: 'app',
  components: {
    FlashCard,
    NewQuestion
  },
  data: function(){
    return {
      question: "What type of pet is an Abyssinian?",
      answer: "A cat.",
      pairs: [],
      categories: [],
      addQuestionOpen: false
    }
  },
  computed:{
    numPairs: function(){
      return this.pairs.length
    },
    randomNum: function(){
      return getRandomInt(0, this.numPairs)
    }
  },
  methods: {
    refresh: function(){
      if(this.numPairs === 0){
        this.question = "What do you need now?"
        this.answer = "More trivia questions!"
      }else{
        let newPair = this.pairs[this.randomNum]
        this.pairs.splice(this.randomNum, 1)
        this.question = newPair[0]
        this.answer = newPair[1]
      }
    }
  },
  mounted() {
    this.$axios.defaults.headers.common['Content-Type'] = 'application/x-www-form-urlencoded';
    this.$axios
      .get('https://sheets.googleapis.com/v4/spreadsheets/1ve72QEH0xQUZ6pA-NRnXxIjcGHBB11TSRjp_ajyYGD0/values/A1:D500?key=AIzaSyAH7rwwBBAjei-sitU8GpXXmEw-kuNXBWY')
      .then(response => {
        this.pairs = response.data.values
        this.refresh()
      })
    this.$axios
      .get('https://sheets.googleapis.com/v4/spreadsheets/1ve72QEH0xQUZ6pA-NRnXxIjcGHBB11TSRjp_ajyYGD0/values/E1:E20?key=AIzaSyAH7rwwBBAjei-sitU8GpXXmEw-kuNXBWY')
      .then(response => {
        this.categories = response.data.values
      })
  },
  created(){
    // on dektop hit Enter to bring up New Question dialog
    window.addEventListener('keyup', (e) => {
      if(e.key == "Enter"){
        this.addQuestionOpen = true
      }
      if(e.key == "Escape"){
        this.addQuestionOpen = false
      }
    })
    // on mobile use long touch
    let touchStartTime = 0,
        touchEndTime = 0,
        touchThresholdTime = 300
    window.addEventListener('touchstart', () => {
      touchStartTime = new Date().getTime()
    })
    window.addEventListener('touchend', () => {
      touchEndTime = new Date().getTime()
      if (touchEndTime - touchStartTime > touchThresholdTime){
        this.addQuestionOpen = true
      }
    })
  }

}
</script>

<style lang="scss">
  * {
    box-sizing: border-box;
    }
  body{
    /* http://www.colourlovers.com/palette/4216399/Pea_Soup */
    --background: #E6EBDA;
    --background-light: #f6fbe8;
    --front: #97D8C0;
    --back: #DAD97D;
    --text: #465B6B;
    --border: #95A39D;
    --error: #fd713d;	
    
    margin: 0;
    background: var(--background);
    font-family: 'Arvo';
    font-size: 2em;
    color: var(--text);
  }
  .modal-backdrop{
    position: fixed;
    top: 0; right: 0; bottom: 0; left: 0;
    background: #ccc;
    justify-content: center;
    align-items: center;
    display: none;
  }
  .modal-backdrop.open{
    display: flex;
  }
  .modal-dialog{
    background: var(--background-light);
    border-radius: 1em;
  } 
  form{
    margin: 1em;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
  } 
  .label, .label span{
    display: block;
  }
  .label + .label{
    padding-top: .5em;
  }
  .radio-group{
    display: flex;
    flex-direction: row;
  }
  .radio{
    display: flex;
    flex-direction: column;
    flex: 1 1 auto;
    align-items: center;
  }
  .radio label{
    order: 2;
  }
  input[type="text"]{
    font-family: inherit;
    min-width: 20em;
    padding: .4em .5em;
    border-radius: .2em;
    border: 0;
    font-size: .5em;
    box-shadow: inset 1px 1px 1px rgba(0,0,0,.1);
  }
  button[type="submit"]{
    margin-top: .5em;
    font-family: inherit;
    font-size: inherit;
    border-radius: .5em;
    color: var(--background);
    background-color: var(--text);
  }
  .errors{
    color: var(--error);
    font-size: .5em;

  }
  /* https://davidwalsh.name/demo/css-flip.php */
  /* entire container, keeps perspective */
  .flip-container {
    margin: 20vh auto;
    
    -webkit-perspective: 1000;
    -moz-perspective: 1000;
    -ms-perspective: 1000;
    perspective: 1000;
    
    -ms-transform: perspective(1000px);
    -moz-transform: perspective(1000px);
    
    -moz-transform-style: preserve-3d;
    -ms-transform-style: preserve-3d;
    }

  /* flip the pane  */
  .flip-container.flipped .flipper{
    -webkit-transform: rotateY(180deg);
    -moz-transform: rotateY(180deg);
    -o-transform: rotateY(180deg);
    transform: rotateY(180deg);
    }

  .flip-container, .front, .back {
    width: 30vw;
    height: 60vh;
    border-radius: 1em;
    }
    @media only screen and (max-width: 768px) {
      .flip-container{
        margin: 6vh auto 14vh auto;
      }
      .flip-container, .front, .back {
        width: 80vw;
        height: 80vh;
      }
    }
  .front, .back {
    text-align: center;
    padding: 1em;
    border: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    }

  /* flip speed goes here */
  .flipper {
      position: relative;
      -webkit-transition: 700ms;
      -webkit-transform-style: preserve-3d;
      -ms-transition: 700ms;
      -moz-transition: 700ms;
      -moz-transform: perspective(1000px);
      -moz-transform-style: preserve-3d;
      -ms-transform-style: preserve-3d;
      transition: 700ms;
      transform-style: preserve-3d;
  }

  /* hide back of pane during swap */
  .front, .back {
      position: absolute;
      top: 0;
      left: 0;
      -webkit-backface-visibility: hidden;
      -moz-backface-visibility: hidden;
      -ms-backface-visibility: hidden;
      backface-visibility: hidden;
    
      -webkit-transition: 600ms;
      -webkit-transform-style: preserve-3d;
      -webkit-transform: rotateY(0deg);
      -moz-transition: 700ms;
      -moz-transform-style: preserve-3d;
      -moz-transform: rotateY(0deg);
      -o-transition: 700ms;
      -o-transform-style: preserve-3d;
      -o-transform: rotateY(0deg);
      -ms-transition: 700ms;
      -ms-transform-style: preserve-3d;
      -ms-transform: rotateY(0deg);
      transition: 700ms;
      transform-style: preserve-3d;
      transform: rotateY(0deg);
  }

  /* front pane, placed above back */
  .front {
    z-index: 2;
    /* for firefox 31 */
    -webkit-transform: rotateY(0deg);
    -ms-transform: rotateY(0deg);
    background-color: var(--front);
  }

  /* back, initially hidden pane */
  .back {
    -webkit-transform: rotateY(-180deg);
    -moz-transform: rotateY(-180deg);
    -o-transform: rotateY(-180deg);
    -ms-transform: rotateY(-180deg);
    transform: rotateY(-180deg);
    background-color: var(--back);
  }
  .refresh{
    display: block;
    margin: -1.5em auto 0 auto;
    width: 50vw;
    max-width: 8em;
    height: 1.5em;
    border: 0;
    border-radius: .5em .5em 0 0;
    color: var(--background);
    background-color: var(--text);
    font-size: inherit;
    font-family: inherit;
    cursor: pointer;
    outline: none;
    }
    .post{
      position: absolute;
      top: 0;
      left: 0;
    }
</style>
