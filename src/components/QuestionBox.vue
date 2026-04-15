<template>
  <div class="qbox-container">
    <b-jumbotron>
      <template #lead>
        <span id="main-question">{{ currentQuestion.question }}</span>
      </template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectedAnswer(index)"
          :class="answerClass(index)"
          :aria-pressed="selectedIndex === index"
          :disabled="answered"
          >{{ answer }}</b-list-group-item
        >
      </b-list-group>

      <b-button
        @click="submitAnswer"
        variant="primary"
        type="submit"
        :disabled="selectedIndex === null || answered"
        >Submit</b-button
      >
      <b-button type="button" @click="$emit('next')" variant="success">
        {{ isLastQuestion ? "Finish" : "Next" }}
      </b-button>

      <p v-if="answered" class="result-label">
        {{ isAnswerCorrect ? "Correct answer." : "Incorrect answer." }}
      </p>
      <p v-else class="result-label">Select an option and submit your answer.</p>
      <p class="question-meta">
        Question {{ questionNumber }} of {{ totalQuestions }}
      </p>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: {
      type: Object,
      required: true,
    },
    isLastQuestion: {
      type: Boolean,
      default: false,
    },
    questionNumber: {
      type: Number,
      default: 1,
    },
    totalQuestions: {
      type: Number,
      default: 1,
    },
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
    };
  },
  computed: {
    isAnswerCorrect() {
      return this.selectedIndex === this.correctIndex;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  methods: {
    selectedAnswer(index) {
      if (this.answered) {
        return;
      }
      this.selectedIndex = index;
    },
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
    submitAnswer() {
      const isCorrect = this.selectedIndex === this.correctIndex;
      this.answered = true;
      this.$emit("answer-submitted", isCorrect);
    },
    answerClass(index) {
      let answerClass = [];
      if (!this.answered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.answered && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      }

      return answerClass;
    },
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
  padding: 0 15%;
}
.list-group-item:hover {
  background-color: #eee;
  cursor: pointer;
}
.btn {
  margin: 0 5px;
}
.selected {
  background-color: rgba(37, 131, 255, 0.603);
}
.correct {
  background-color: rgb(92, 189, 92);
}
.incorrect {
  background-color: rgba(255, 43, 43, 0.979);
}
.result-label {
  margin-top: 15px;
  margin-bottom: 0;
  font-weight: 600;
}
.question-meta {
  margin-top: 8px;
  margin-bottom: 0;
  color: #6c757d;
}
</style>
