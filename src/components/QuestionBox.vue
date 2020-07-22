<template>
  <div>
    <b-card no-body class="my-4" bg-variant="light" text-variant="dark">
      <b-card-header v-html="currentQuestion.question"></b-card-header>
      <b-card-body>
        <b-list-group class="mb-4">
          <fieldset
            class="list-group-item border p-2"
            v-for="(answer, index) in shuffledAnswers"
            :key="answer"
            :class="answerClass(index)"
            @click.prevent="selectAnswer(index)"
          >
            <legend class="w-auto mb-0" v-if="!isAnswered && selectedIndex === index">You Select</legend>
            <legend
              class="w-auto mb-0"
              v-else-if="isAnswered && correctIndex === index && selectedIndex === index"
            >You Succeed</legend>
            <legend
              class="w-auto mb-0"
              v-else-if="isAnswered && selectedIndex !== index && correctIndex === index"
            >You Missed</legend>
            <legend
              class="w-auto mb-0"
              v-else-if="isAnswered && selectedIndex === index && correctIndex !== index"
            >Your Answer</legend>
            <div class="d-flex justify-content-start align-items-center text-left">
              <b-badge class="mr-3" variant="secondary" pill v-text="index + 1">1</b-badge>
              <span v-html="answer"></span>
            </div>
          </fieldset>
        </b-list-group>
        <div class="answer-button-group d-flex align-items-center py-4">
          <b-button
            variant="outline-primary"
            :disabled="selectedIndex === null || isAnswered"
            @click="handleSubmitAnswer"
          >Submit</b-button>
          <b-button
            variant="outline-secondary"
            @click="nextIndex"
            :disabled="(currentIndex === lastIndex) || !isAnswered"
          >Next</b-button>
        </div>
      </b-card-body>
    </b-card>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    currentIndex: Number,
    nextIndex: Function,
    lastIndex: Number,
    incrementCounter: Function,
  },
  data: function () {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      isAnswered: false,
    };
  },
  computed: {
    // not used since answer need to shuffle
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  watch: {
    selectAns(index) {
      this.selectedIndex = index;
    },
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.isAnswered = false;
        this.shuffleAnswers();
      },
    },
  },
  methods: {
    selectAnswer(index) {
      if (!this.isAnswered) {
        this.selectedIndex = index;
      }
    },
    handleSubmitAnswer() {
      if (this.selectedIndex === null) {
        return;
      }
      let isCorrect = false;

      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }

      this.isAnswered = true;
      this.incrementCounter(isCorrect);
    },
    handleLegend() {},
    shuffleAnswers() {
      const answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answeredVariant(index) {
      let variant = "light";

      if (!this.isAnswered && this.selectedIndex === index) {
        variant = "primary";
      } else if (this.isAnswered && this.correctIndex === index) {
        variant = "success";
      } else if (
        this.isAnswered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        variant = "danger";
      }
      return variant;
    },
    answerClass(index) {
      let variant = "list-group-item-light";

      if (!this.isAnswered && this.selectedIndex === index) {
        variant = "list-group-item-primary";
      } else if (this.isAnswered && this.correctIndex === index) {
        variant = "border-success";
      } else if (
        this.isAnswered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        variant = "border-danger";
      }
      return variant;
    },
  },
};
</script>

<style scoped>
.card-header {
  font-weight: bold;
  font-size: 17px;
}
.answer-button-group {
  justify-content: space-evenly;
}
.list-group-item {
  cursor: pointer;
}
fieldset {
  position: relative;
  display: block;
  padding: 0.75rem 1.25rem;
  background-color: #fff;
  border: 1px solid rgba(0, 0, 0, 0.125);
  border-radius: 1rem !important;
}

fieldset + fieldset {
  margin-top: 15px;
}
legend {
  font-size: 14px;
  text-align: right;
  padding: 0 5px;
  line-height: 6px;
}
</style>
