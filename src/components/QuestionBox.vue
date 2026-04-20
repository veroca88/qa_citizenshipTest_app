<template>
  <section class="card shadow-sm border-0">
    <div class="card-body p-4 p-md-5">
      <p id="main-question" class="lead fw-semibold mb-4">
        {{ currentQuestion.question }}
      </p>

      <div class="list-group mb-4">
        <button
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          type="button"
          class="list-group-item list-group-item-action text-start"
          :class="answerClass(index)"
          :aria-pressed="selectedIndex === index"
          :disabled="answered"
          @click="selectedAnswer(index)"
        >
          {{ answer }}
        </button>
      </div>

      <div class="d-flex justify-content-center gap-2 flex-wrap">
        <button
          type="button"
          class="btn btn-primary"
          :disabled="selectedIndex === null || answered"
          @click="submitAnswer"
        >
          Submit
        </button>
        <button
          type="button"
          class="btn btn-success"
          @click="$emit('next')"
        >
          {{ isLastQuestion ? "Finish" : "Next" }}
        </button>
      </div>

      <p v-if="answered" class="result-label mt-3 mb-0 fw-semibold">
        {{ isAnswerCorrect ? "Correct answer." : "Incorrect answer." }}
      </p>
      <p v-else class="result-label mt-3 mb-0 fw-semibold">
        Select an option and submit your answer.
      </p>
      <p class="question-meta mt-2 mb-0 text-secondary">
        Question {{ questionNumber }} of {{ totalQuestions }}
      </p>
    </div>
  </section>
</template>

<script>
const shuffleAnswers = (answers) => {
  const shuffled = [...answers];

  for (let i = shuffled.length - 1; i > 0; i -= 1) {
    const randomIndex = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[randomIndex]] = [shuffled[randomIndex], shuffled[i]];
  }

  return shuffled;
};

export default {
  emits: ["next", "answer-submitted"],
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
      this.shuffledAnswers = shuffleAnswers(answers);
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
      if (!this.answered && this.selectedIndex === index) {
        return "selected";
      }

      if (this.answered && this.correctIndex === index) {
        return "correct";
      }

      if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        return "incorrect";
      }

      return "";
    },
  },
};
</script>

<style scoped>
.selected {
  background-color: rgba(37, 131, 255, 0.22);
}
.correct {
  background-color: rgba(92, 189, 92, 0.3);
}
.incorrect {
  background-color: rgba(255, 43, 43, 0.22);
}
</style>
