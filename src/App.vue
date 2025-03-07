<template>
  <div>
    <template v-if="this.question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer,index) in this.answers" :key="index">
        <input
          v-model="this.chosenAnswer"
          :value="answer"
          :disabled="this.answerSubmitted"
          name="options"
          type="radio"
        >
        <label v-html="answer"></label><br>
      </template>

      <button
        class="send"
        type="button"
        @click="submitAnswer"
        v-if="!this.answerSubmitted"
      >
        Send
      </button>

      <section class="result" v-if="this.answerSubmitted">
        <h4 v-if="this.chosenAnswer === this.correctAnswer">
          &#9989; Congratulations, the answer "{{this.chosenAnswer}}" is correct
        </h4>
        <h4 v-else>
          &#10060; I'm sorry, you picked the wrong answer. The correct is "{{this.chosenAnswer}}"
        </h4>
        <button class="send" type="button">Next question</button>
      </section>
    </template>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {},
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false
    }
  },
  computed: {
    answers() {
      const answers = [...this.incorrectAnswers];
      const position = Math.round(Math.random() * answers.length);

      answers.splice(position, 0, this.correctAnswer);

      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert('Pick one of the options');
        return;
      }

      this.answerSubmitted = true;

      this.chosenAnswer === this.correctAnswer
        ? console.log('You got it right')
        : console.log('You got it wrong');
    }
  },
  created() {
    this.axios
      .get('https://opentdb.com/api.php?amount=1&category=11')
      .then((response) => {
        const results = response.data.results[0];

        this.question = results.question;
        this.incorrectAnswers = results.incorrect_answers;
        this.correctAnswer = results.correct_answer;
      })
  }
}
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
