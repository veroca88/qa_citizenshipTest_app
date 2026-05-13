<template>
  <header class="border-bottom bg-white sticky-top">
    <div class="container py-3 d-flex flex-column flex-md-row justify-content-between align-items-center gap-2">
      <h1 class="h4 mb-0">Citizenship Quiz App</h1>
      <p class="mb-0 fw-semibold">
        Score: {{ numCorrect }}/{{ numTotal }}
        <span v-if="questionCount" class="text-secondary">
          ({{ questionCount }} questions)
        </span>
      </p>
    </div>
  </header>
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
