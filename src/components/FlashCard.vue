<template>
<div>
  <div class="flip-container" :class="{flipped: flipped}" @click="flip">
   <div class="flipper" ref="flashcard" id="flashcard">
    <div class="front">
      <transition name="fade-in-out" mode="out-in">
        <span id="flashcard--content_q" :key="prompt">
            {{ prompt }}
        </span>
      </transition>
    </div>
    <div class="back">
      <span id="flashcard--content_a">{{ response }}</span>
    </div>
    </div>
  </div>
  <button class="button refresh" @click="requestNewCard">Next</button>
</div>
</template>

<script>
  export default {
    props: {
      prompt: {
        required: true,
        type: String
      },
      response: {
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
        return new Promise((resolve) => {
          const el = this.$refs.flashcard
          const onAnimationComplete = () => {
            el.removeEventListener('transitionend', onAnimationComplete)
            resolve();
          }
          el.addEventListener('transitionend', onAnimationComplete, false)
          this.flipped = !this.flipped
        })
      },
      requestNewCard() {
        if(this.flipped){
          this.flip().then(() => {
            this.$emit('request-new-card')
          })
        } else {
          this.$emit('request-new-card')
        }
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