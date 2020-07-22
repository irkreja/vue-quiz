<template>
  <div>
    <b-navbar toggleable="lg" type="light" variant="light">
      <b-navbar-brand class="mx-auto">
        Vue Quiz App
        <small
          class="d-block"
        >Counter: {{ numCorrect }}/{{ numTotal }} | Remaining Question: {{ totalQs - numTotal }}</small>
      </b-navbar-brand>
    </b-navbar>
  </div>
</template>

<script>
export default {
  props: ["numTotal", "numCorrect", "totalQs"],
};
</script>

<style>
</style>