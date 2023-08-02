<template>
  <div class="container">
    <h1 class="text-center text-success">Translate It</h1>
    <br />
    <div class="row">
      <div class="col-md-12 text-center"></div>
      <TranslateForm
        @translatedEvent="translatedText = $event"
        @historyEvent="history.push($event)"
      />
      <h1 class="text-success text-center">{{ translatedText }}</h1>
      <SearchHistory :history="history" />
    </div>
  </div>
</template>

<script>
import TranslateForm from "./components/TranslateForm.vue";
import SearchHistory from "./components/SearchHistory.vue";
export default {
  data() {
    return {
      translatedText: "",
      history: [],
    };
  },
  components: {
    TranslateForm,
    SearchHistory,
  },
};
</script>

<style></style>
