<script>
  export default {
    // props: {
    //   prompt: {
    //     required: true,
    //     type: String
    //   },
    //   response: {
    //     required: true
    //   }
    // },
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
      }
      // refresh() {
      //   this.flipped = false
      //   // this could be done with events and promises?
      //   // if the flip function returned a promise when it was complete?
      //   setTimeout(() => {
      //     this.$emit('refresh')
      //   }, this.timing)
      // }
    },
    render() {
      return this.$scopedSlots.default({
        // prompt: this.prompt,
        // response: this.response,
        flipped: this.flipped,
        flip: this.flip
        });
    }
  }
</script>