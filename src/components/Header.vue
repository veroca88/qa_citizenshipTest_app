<template>
  <div>
    <b-nav tabs align="center">
      <b-nav-item disabled><b>Citizenship Quiz App</b></b-nav-item>
      <b-nav-item disabled>
        Score: {{ numCorrect }}/{{ numTotal }}
        <span v-if="questionCount">({{ questionCount }} questions)</span>
      </b-nav-item>
    </b-nav>
  </div>
</template>

<script>
export default {
  props: {
    numCorrect: {
      type: Number,
      required: true,
    },
    numTotal: {
      type: Number,
      required: true,
    },
    questionCount: {
      type: Number,
      default: 0,
    },
  },
};
</script>

<style>
.nav {
  font-size: calc(1em + 1vw);
}
</style>
