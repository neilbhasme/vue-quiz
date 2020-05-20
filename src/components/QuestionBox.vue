
<template>
   <div class = "Question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4">
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
       
      </b-list-group>

      <b-button 
        @click="submitAnswer" 
        variant="primary"
        :disabled= "selectedIndex === null || answered">
        Submit
      </b-button>

      <b-button 
        @click="incrementIndexQuestion" 
        variant="success"
        :disabled= "selectedIndex === null || !answered">
        Next
      </b-button>

    </b-jumbotron>
  </div>
</template>


<script>
import _ from 'lodash'
export default {
  props: {
    currentQuestion: Object,
    incrementIndexQuestion: Function,
    increment: Function
  },
  data() {
      return {
        selectedIndex: null,
        correctIndex: null, 
        shuffledAnswers: [],
        answered: false
      }
  },
  computed: {
    formulateAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,  //true makes call it when the prop is first created and everytime when the prop is updated (latter is done anyway in watch, but former ie because of immediate = true
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers() 
      }
    }
//    () {   //does not shuffle on first attempt before next is clicked if currentQuestion is a function, istead we make it an object as above
//      this.selectedIndex = null
 //     this.shuffleAnswers()
 //   }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index
      console.log(index)
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer() {
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)
    },
    answerClass(index) {
      let answerClass = ''
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } else if (this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = 'incorrect'
      }
      return answerClass
    }
  }
}
</script>

<style scoped>

.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: red;
}

</style>