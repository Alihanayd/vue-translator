<template>
  <div class="well" v-if="translatedText != ''">
    <div>
      <strong class="text-white"
        >{{ history[0].from }}
        <i class="fa fa-chevron-right mx-2 text-white fs-5"></i>
        {{ history[0].to }}</strong
      >
    </div>
    <h1 class="text-center text-warning">
      {{ translatedText }}
      <span class="ms-4"><i @click="copyText" class="fa fa-copy"></i></span>
    </h1>
  </div>
</template>

<script>
export default {
  props: ["translatedText", "history"],
  methods: {
    copyText() {
      navigator.clipboard.writeText(this.translatedText);
      console.log(this.history);
    },
  },
};
</script>

<style lang="scss" scoped></style>
