<script>
  export default {
    data() {
      return {
        flipped: false
      }
    },
    computed: {

    },
    methods: {
      flip() {
        this.flipped = !this.flipped
      },
      requestNewCard() {
        this.flipped = false
        // this could be done with events and promises?
        // if the flip function returned a promise when it was complete?
        setTimeout(() => {
          this.$emit('request-new-card')
        }, 300)
      }
    },
    render() {
      return this.$scopedSlots.default({
        flipped: this.flipped,
        flip: this.flip,
        requestNewCard: this.requestNewCard
        });
    }
  }
</script>