<template>
  <div>
    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount"/>
    <template v-if="this.question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer,index) in this.answers" :key="index">
        <input
          v-model="this.chosenAnswer"
          :value="answer"
          :disabled="this.answerSubmitted"
          name="options"
          type="radio"
          :id="index"
        >
        <label :for="index" v-html="answer"></label><br>
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
        <h4
          v-if="this.chosenAnswer === this.correctAnswer"
          v-html="'&#9989; Congratulations, the answer &rdquo;' + this.chosenAnswer + '&ldquo; is correct'"
        >
        </h4>
        <h4
          v-else
          v-html="'&#10060; I\'m sorry, you picked the wrong answer. The correct is &rdquo;' + this.correctAnswer + '&ldquo;'"
        >
        </h4>
        <button class="send" type="button" @click="getNewQuestion">Next question</button>
      </section>
    </template>
  </div>
</template>

<script>
import ScoreBoard from "@/components/ScoreBoard.vue";

export default {
  name: 'App',
  components: {ScoreBoard},
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
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
        ? this.winCount++
        : this.loseCount++;
    },

    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios
        .get('https://opentdb.com/api.php?amount=1&category=11')
        .then((response) => {
          const results = response.data.results[0];

          this.question = results.question;
          this.incorrectAnswers = results.incorrect_answers;
          this.correctAnswer = results.correct_answer;
        });
    }
  },
  created() {
    this.getNewQuestion();
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
