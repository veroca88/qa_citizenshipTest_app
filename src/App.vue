<template>
  <div id="app">
    <Header />

    <b-container class="bv-example-row">
      <b-row>
        <b-col>
          <QBox :currentQuestion="questions[index]" :next="next" />
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
    };
  },
  methods: {
    next() {
      this.index++;
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
