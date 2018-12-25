<template>
    <div class="modal-backdrop">
      <div class="modal-dialog">
        <button class="button close" @click="close">X</button>
        <form @submit.prevent="validateAndSubmit">
          <ul class="errors" v-if="errors.length">
            <li v-for="(error, index) in errors" v-bind:key="index">{{ error }}</li>
          </ul>
          <label  class="label" for="question"><span>question</span>
            <input name="question" type="text" v-model="question" />
          </label>
          <label  class="label" for="answer"><span>answer</span>
            <input name="answer" type="text" v-model="answer" />
          </label>
          <label  class="label" for="category"><span>category</span>
            <select name="category" id="category" v-model="category">
              <option v-for="(category, index) in categories" :value="category" :key="index">{{ category }}</option>
            </select>
            <!-- <input name="category" type="text" v-model="category" /> -->
          </label>
          <span class="label"><span>difficulty</span>
            <div class="radio-group">
              <div class="radio">
                <label for="difficulty-1">1</label>
                <input type="radio" name="difficulty" id="difficulty-1" value = "1" v-model="difficulty" />
              </div>
              <div class="radio">
                <label for="difficulty-2">2</label>
                <input type="radio" name="difficulty" id="difficulty-2" value = "2" v-model="difficulty" />
              </div>
              <div class="radio">
                <label for="difficulty-3">3</label>
                <input type="radio" name="difficulty" id="difficulty-3" value = "3" v-model="difficulty" />
              </div>
            </div>
    
          </span>
          <button class="button" type="submit">Add it!</button>
        </form>
      </div>
    </div>
</template>

<script>
  export default {
    props: {
      categories: {
        required: false,
        type: Array
      }
    },
    data: function(){
      return {
        question: '',
        answer: '',
        category: '',
        difficulty: null,
        errors: []
      }
    },
    methods: {
      validateAndSubmit(){
        this.errors = []
        if(this.question.length >= 15 && this.answer !== ""){
          this.addNew()
        }else{
          if(this.question.length < 15){
            this.errors.push("Please provide a question.")
          }
          if(this.answer === ""){
            this.errors.push("Please provide an answer.")
          }
        }
      },
      addNew() {
        this.$axios
          .post('https://hooks.zapier.com/hooks/catch/3852402/03flcx/',{
            question: this.question,
            answer: this.answer,
            category: this.category,
            difficulty: this.difficulty
          },{
            headers: {
              'Content-Type': 'application/x-www-form-urlencoded'
            }
          })
          // TODO add feedback success or failure
          // TODO add option to add another or continue with game
          // TODO add question to extant `pairs` array
          // TODO add category to extant `categories` array
          .then(() => {
            this.question = ''
            this.answer = ''
            this.category = ''
            this.difficulty = null
            this.$emit('addSuccess')
          })
          .catch(function (error) {
            alert('Darn, the following went wrong: ' + error)
          });
      },
      close() {
        this.$emit('addCancel')
      }
    }
  }
</script>

<style scoped>

</style>