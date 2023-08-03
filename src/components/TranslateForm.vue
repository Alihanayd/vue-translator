<template>
  <form @submit="translate" class="well">
    <div class="form-group has-validation">
      <textarea
        v-model="toTranslateText"
        cols="30"
        rows="5"
        class="form-control"
        placeholder="Write a word or sentence to translate"
        :class="{ 'is-invalid': isValidText }"
      ></textarea>
      <div class="invalid-feedback" v-if="isValidText">
        Please enter a text to translate.
      </div>
      <label for=""></label>
      <select
        v-model="translateTo"
        class="form-control"
        :class="{ 'is-invalid': isValidLang }"
      >
        <option disabled selected value="">
          Select the language you want to translate.
        </option>
        <option
          v-for="(language, index) in languages"
          :key="index"
          :value="language.code"
        >
          {{ language.name }}
        </option>
      </select>
      <div class="invalid-feedback" v-if="isValidLang">
        Please select a language.
      </div>
    </div>
    <br />
    <div class="text-left" v-if="translatedLangName">
      <strong>Detected Language : {{ detectedLanguage }} </strong>
    </div>
    <br />
    <button
      @mouseover="setShowIcon(true)"
      @mouseleave="setShowIcon(false)"
      type="submit"
      class="btn btn-primary btn-block"
    >
      <Transition mode="out-in">
        <span v-if="!showIcon">Translate</span>
        <i v-else class="fa fa-language"></i>
      </Transition>
    </button>
  </form>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      toTranslateText: "",
      translateTo: "",
      translatedLangName: "",
      languages: [],
      translatedText: "",
      detectedLanguage: "",
      isValidText: false,
      isValidLang: false,
      showIcon: false,
    };
  },
  props: ["translatedEvent"],
  methods: {
    setShowIcon(value) {
      this.showIcon = value;
    },
    checkError() {
      this.resetErrors();
      if (this.toTranslateText === "") {
        this.isValidText = true;
        return true;
      }
      if (this.translateTo === "") {
        this.isValidLang = true;
        return true;
      }
      return false;
    },
    resetErrors() {
      this.isValidText = false;
      this.isValidLang = false;
    },
    translate(e) {
      e.preventDefault();
      if (this.checkError()) return;
      const encodedParams = new URLSearchParams();
      encodedParams.set("source_language", "auto");
      encodedParams.set("target_language", this.translateTo);
      encodedParams.set("text", this.toTranslateText);
      axios
        .post(
          "https://text-translator2.p.rapidapi.com/translate",
          encodedParams,
          {
            headers: {
              "content-type": "application/x-www-form-urlencoded",
              "X-RapidAPI-Key": process.env.VUE_APP_API_KEY,
              "X-RapidAPI-Host": "text-translator2.p.rapidapi.com",
            },
          }
        )
        .then((response) => {
          this.translatedText = response.data.data.translatedText;
          this.detectedLanguage =
            response.data.data.detectedSourceLanguage.name;

          const result = this.languages.find(
            (language) => language.code === this.translateTo
          );
          this.translatedLangName = result.name;

          this.$emit("translatedEvent", this.translatedText);
          let history = {
            from: this.detectedLanguage,
            to: this.translatedLangName,
            text: this.toTranslateText,
            translatedText: this.translatedText,
          };
          this.$emit("historyEvent", history);
        })
        .catch((error) => {
          console.error(error);
        });
    },
  },
  created() {
    axios
      .get("https://text-translator2.p.rapidapi.com/getLanguages", {
        headers: {
          "X-RapidAPI-Key": process.env.VUE_APP_API_KEY,
          "X-RapidAPI-Host": "text-translator2.p.rapidapi.com",
        },
      })
      .then((response) => {
        this.languages = response.data.data.languages;
      })
      .catch((error) => {
        console.error(error);
      });
  },
};
</script>

<style scoped>
.is-invalid {
  border: 2px solid #e74c3c;
}
.invalid-feedback {
  color: #e74c3c;
  font-size: small;
}
.v-enter-active,
.v-leave-active {
  transition: opacity 0.2s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
