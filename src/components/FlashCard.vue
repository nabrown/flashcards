<template>
<div>
  <div class="flip-container" :class="{flipped: flipped}" @click="flip">
   <div class="flipper" ref="flashcard" id="flashcard">
    <div class="front">
      <transition name="fade-in-out" mode="out-in">
        <span id="flashcard--content_q" :key="question">
            {{ question }}
        </span>
      </transition>
    </div>
    <div class="back">
      <span id="flashcard--content_a">{{ answer }}</span>
    </div>
    </div>
  </div>
  <button class="refresh" @click.stop.prevent="refresh">Next</button>
</div>
</template>

<script>
  export default {
    props: {
      question: {
        required: true,
        type: String
      },
      answer: {
        required: true
      }
    },
    data() {
      return {
        flipped: false
      }
    },
    computed: {
      timing() {
        return (window.getComputedStyle(this.$refs.flashcard).transitionDuration.replace(/s/, '') * 1000) / 2
      }
    },
    methods: {
      flip() {
        this.flipped = !this.flipped
      },
      refresh() {
        this.flipped = false
        // this could be done with events and promises?
        // if the flip function returned a promise when it was complete?
        setTimeout(() => {
          this.$emit('refresh')
        }, this.timing)
      }
    }
  }
</script>

<style scoped>
.fade-in-out-enter-active,
.fade-in-out-leave-active{
  transition: opacity .6s ease;
}
.fade-in-out-enter, .fade-in-out-leave-to{
  opacity: 0;
}
</style>