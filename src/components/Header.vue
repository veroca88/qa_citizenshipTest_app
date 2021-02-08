<template>
  <div>
    <b-nav tabs align="center">
      <b-nav-item disabled><b>Citizenship Quiz App</b></b-nav-item>
      <i class="fas fa-flag-usa fa-2x"></i>
      <b-nav-item disabled>Counter: {{ numCorrect }}/{{ numTotal }}</b-nav-item>
    </b-nav>
  </div>
</template>

<script>
export default {
  props: ["numCorrect", "numTotal"],
};
</script>

<style>
.fas {
  color: rgb(238, 185, 38);
}
.nav {
  font-size: calc(1em + 1vw);
}
</style>