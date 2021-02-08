<template>
  <div id="app">
    <Header :numCorrect="numCorrect" :numTotal="numTotal" />

    <b-container class="bv-example-row">
      <b-row>
        <b-col>
          <QBox
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :next="next"
            :increment="increment"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import QBox from "./components/QuestionBox.vue";

export default {
  name: "app",
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
    };
  },
  methods: {
    next() {
      this.index++;
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect++;
      }
      this.numTotal++;
    },
  },
  mounted: function () {
    fetch("https://veroca88.github.io/Data/qa_database.json", {
      method: "get",
    })
      .then((response) => {
        return response.json();
      })
      .then((jsonData) => {
        this.questions = jsonData.results;
      });
  },
};
</script>

<style>
#app {
  margin-top: 60px;
  text-align: center;
}
</style>
