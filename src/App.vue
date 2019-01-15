<template>
  <div id="app">
    <flash-card :prompt="prompt" :response="response" @request-new-card="updateCard"></flash-card>
    <!-- <flash-card-renderless @request-new-card="updateCard">
      <div slot-scope="{flip, flipped, requestNewCard}">
        <div class="flip-container" :class="{flipped: flipped}" @click="flip">
          <div class="flipper" ref="flashcard" id="flashcard">
            <div class="front">
              <transition name="fade-in-out" mode="out-in">
                <span id="flashcard--content_q" :key="prompt">{{ prompt }}</span>
              </transition>
            </div>
            <div class="back">
              <span id="flashcard--content_a">{{ response }}</span>
            </div>
          </div>
        </div>
        <button class="button refresh" @click="requestNewCard">Next</button>
      </div>
    </flash-card-renderless> -->
    
    <new-card-form
      :class="{'open': addCardOpen}"
      @addSuccess="addCardOpen = false"
      @addCancel="addCardOpen = false"
      :categories="categories"
    ></new-card-form>
  </div>
</template>

<script>
import FlashCard from "./components/FlashCard.vue";
// import FlashCardRenderless from "./components/FlashCardRenderless.vue";
import NewCardForm from "./components/NewCardForm.vue";
import {CONFIG} from "./app-config.js";

function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}

export default {
  name: "app",
  components: {
    FlashCard,
    // FlashCardRenderless,
    NewCardForm
  },
  data: function() {
    return {
      outOfCardsPrompt: "What do you need now?",
      outOfCardsResponse: "More trivia!",
      prompt: "",
      response: "",
      cards: [],
      categories: [],
      addCardOpen: false
    };
  },
  computed: {
    numCards: function() {
      return this.cards.length;
    },
    randomNum: function() {
      return getRandomInt(0, this.numCards);
    }
  },
  methods: {
    updateCard: function() {
      if (this.numCards === 0) {
        this.prompt = this.outOfCardsPrompt;
        this.response = this.outOfCardsResponse;
      } else {
        let newCard = this.cards[this.randomNum];
        this.cards.splice(this.randomNum, 1);
        this.prompt = newCard[0];
        this.response = newCard[1];
      }
    }
  },
  mounted() {
    //https://zapier.com/help/common-problems-webhooks/
    this.$axios.defaults.headers.common["Content-Type"] =
      "application/x-www-form-urlencoded";
    this.$axios
      .get(
        "https://sheets.googleapis.com/v4/spreadsheets/1ve72QEH0xQUZ6pA-NRnXxIjcGHBB11TSRjp_ajyYGD0/values/A1:D500?key=" + CONFIG.GoogleAPIKey
      )
      .then(response => {
        this.cards = response.data.values;
        this.updateCard();
      });
    this.$axios
      .get(
        "https://sheets.googleapis.com/v4/spreadsheets/1ve72QEH0xQUZ6pA-NRnXxIjcGHBB11TSRjp_ajyYGD0/values/E1:E20?key=" + CONFIG.GoogleAPIKey
      )
      .then(response => {
        this.categories = response.data.values.map(x => x[0]);
      });
  },
  created() {
    // on dektop hit Enter to bring up New Card Form dialog
    window.addEventListener("keyup", e => {
      if (e.key == "Enter") {
        this.addCardOpen = true;
      }
      if (e.key == "Escape") {
        this.addCardOpen = false;
      }
    });
    // on mobile use long two-fingered touch
    let touchStartTime = 0,
      touchEndTime = 0,
      touchThresholdTime = 300;
    window.addEventListener("touchstart", () => {
      touchStartTime = new Date().getTime();
    });
    window.addEventListener("touchend", e => {
      touchEndTime = new Date().getTime();
      if (
        touchEndTime - touchStartTime > touchThresholdTime &&
        e.touches.length > 1
      ) {
        this.addCardOpen = true;
      }
    });
  }
};
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}
body {
  /* http://www.colourlovers.com/palette/4216399/Pea_Soup */
  --background: #e6ebda;
  --background-light: #f6fbe8;
  --front: #97d8c0;
  --back: #dad97d;
  --text: #465b6b;
  --border: #95a39d;
  --error: #fd713d;

  margin: 0;
  background: var(--background);
  font-family: "Arvo";
  font-size: 2em;
  font-size: calc(28px + 4 * ((100vw - 320px) / 1200));
  color: var(--text);
}
.modal-backdrop {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: #ccc;
  justify-content: center;
  align-items: center;
  display: none;
}
.modal-backdrop.open {
  display: flex;
}
.modal-dialog {
  position: relative;
  background: var(--background-light);
  border-radius: 1em;
}
form {
  margin: 0.8em;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
}
.label,
.label span {
  display: block;
}
.label + .label {
  padding-top: 0.5em;
}
.radio-group {
  display: flex;
  flex-direction: row;
}
.radio {
  display: flex;
  flex-direction: column;
  flex: 1 1 auto;
  align-items: center;
}
.radio label {
  order: 2;
}
input[type="text"],
select {
  font-family: inherit;
  min-width: 20em;
  padding: 0.4em 0.5em;
  border-radius: 0.2em;
  border: 0;
  font-size: 0.5em;
  box-shadow: inset 1px 1px 1px rgba(0, 0, 0, 0.1);
}
input[type="radio"] {
  transform: scale(1.5);
}
@media only screen and (max-width: 768px) {
  input[type="text"],
  select {
    min-width: 18em;
  }
}
.button {
  font-size: inherit;
  border-radius: 0.5em;
  color: var(--background);
  background-color: var(--text);
  cursor: pointer;
  outline: none;
}
button[type="submit"] {
  margin-top: 0.5em;
  font-family: inherit;
}
.errors {
  color: var(--error);
  font-size: 0.5em;
}
.close {
  position: absolute;
  top: -20px;
  right: -8px;
  width: 1.8em;
  height: 1.8em;
  z-index: 99;
  border-radius: 50%;
  font-size: 1em;
  line-height: 1em;
}

/* https://davidwalsh.name/demo/css-flip.php */
/* entire container, keeps perspective */
.flip-container {
  margin: 20vh auto;

  perspective: 1000;

  -ms-transform: perspective(1000px);
  -moz-transform: perspective(1000px);

  -moz-transform-style: preserve-3d;
  -ms-transform-style: preserve-3d;
}

/* flip the pane  */
.flip-container.flipped .flipper {
  transform: rotateY(180deg);
}

.flip-container,
.front,
.back {
  width: 30vw;
  height: 60vh;
  border-radius: 1em;
}
@media only screen and (max-width: 768px) {
  .flip-container {
    margin: 6vh auto auto auto;
  }
  .flip-container,
  .front,
  .back {
    width: 80vw;
    height: 80vh;
  }
}
.front,
.back {
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
  -moz-transform: perspective(1000px);
  transition: 700ms;
  transform-style: preserve-3d;
}

/* hide back of pane during swap */
.front,
.back {
  position: absolute;
  top: 0;
  left: 0;
  backface-visibility: hidden;
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
  transform: rotateY(-180deg);
  background-color: var(--back);
}

.refresh {
  display: block;
  position: fixed;
  width: 6em;
  height: 1.8em;
  border: 0;
  border-radius: 0.5em 0.5em 0 0;
  font-size: 0.5em;
  right: 0;
  bottom: 15em;
  transform: rotate(-90deg);
  transform-origin: 100% 100%;
}
</style>
