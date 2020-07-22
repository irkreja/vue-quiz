<template>
  <div id="app">
    <Header :totalQs="questions.length" :numCorrect="numCorrect" :numTotal="numTotal" />
    <b-container>
      <b-row>
        <b-col md="4" offset-md="4">
          <QuestionBox
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :currentIndex="index"
            :nextIndex="nextIndex"
            :lastIndex="questions.length - 1"
            :incrementCounter="incrementCounter"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from "./components/Header";
import QuestionBox from "./components/QuestionBox";

import defaultQuestions from "./data.json";
export default {
  name: "App",
  components: {
    Header,
    QuestionBox,
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
    };
  },
  methods: {
    nextIndex() {
      if (this.index < this.questions.length - 1) {
        this.index += 1;
      }
    },
    incrementCounter(isCorrect) {
      if (isCorrect) {
        this.numCorrect += 1;
      }
      this.numTotal += 1;
    },
  },
  mounted() {
    fetch(
      "https://opentdb.com/api.php?amount=10&category=18&difficulty=easy&type=multiple",
      {
        method: "GET",
      }
    )
      .then((response) => response.json())
      .then((jsonData) => {
        console.log("Success:", jsonData);
        if (jsonData.response_code === 0) {
          this.questions = jsonData.results;
        }
      })
      .catch((error) => {
        console.error("Error:", error);
        if (defaultQuestions) {
          this.questions = defaultQuestions.results;
        }
      });
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
