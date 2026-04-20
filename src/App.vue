<template>
  <div id="app" class="pb-5">
    <Header
      :numCorrect="numCorrect"
      :numTotal="numTotal"
      :questionCount="questions.length"
    />

    <main class="container py-4">
      <div class="row justify-content-center">
        <div class="col-12 col-lg-10">
          <div v-if="loading" class="alert alert-info" role="status">
            Loading questions...
          </div>
          <div v-if="error" class="alert alert-danger" role="alert">{{ error }}</div>
          <div v-if="warning && !loading && !error" class="alert alert-warning" role="alert">
            {{ warning }}
          </div>

          <section
            v-if="!loading && !error && quizComplete"
            class="p-4 p-md-5 bg-light rounded-3 border"
          >
            <h2 class="h3">Quiz complete</h2>
            <p class="lead">Final score: {{ numCorrect }}/{{ numTotal || questions.length }}</p>
            <button type="button" class="btn btn-primary" @click="restartQuiz">
              Restart quiz
            </button>
          </section>

          <QBox
            v-if="!loading && !error && !quizComplete && currentQuestion"
            :currentQuestion="currentQuestion"
            :isLastQuestion="isLastQuestion"
            :questionNumber="index + 1"
            :totalQuestions="questions.length"
            @next="next"
            @answer-submitted="increment"
          />
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import QBox from "./components/QuestionBox.vue";
import fallbackQuestions from "./assets/questions.json";

export default {
  name: "App",
  components: {
    Header,
    QBox,
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
      loading: true,
      error: null,
      warning: null,
      quizComplete: false,
    };
  },
  computed: {
    currentQuestion() {
      return this.questions[this.index] || null;
    },
    isLastQuestion() {
      return this.index === this.questions.length - 1;
    },
  },
  methods: {
    next() {
      if (this.isLastQuestion) {
        this.quizComplete = true;
        return;
      }

      this.index += 1;
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect += 1;
      }
      this.numTotal += 1;
    },
    restartQuiz() {
      this.index = 0;
      this.numCorrect = 0;
      this.numTotal = 0;
      this.quizComplete = false;
    },
    async loadQuestions() {
      try {
        const response = await fetch("https://veroca88.github.io/Data/qa_database.json");

        if (!response.ok) {
          throw new Error(`Failed to load questions (HTTP ${response.status})`);
        }

        const jsonData = await response.json();
        const remoteQuestions =
          jsonData && Array.isArray(jsonData.results) ? jsonData.results : [];

        this.questions = remoteQuestions.length ? remoteQuestions : fallbackQuestions;

        if (!this.questions.length) {
          this.error = "No questions are available right now.";
        }
      } catch (err) {
        this.questions = fallbackQuestions;
        this.warning = "Using offline fallback questions due to a network issue.";

        if (!this.questions.length) {
          this.error = err.message || "Unable to load questions.";
        }
      } finally {
        this.loading = false;
      }
    },
  },
  mounted() {
    this.loadQuestions();
  },
};
</script>
