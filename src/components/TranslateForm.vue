<template>
  <div class="container">
    <form @submit="translateIt" class="well">
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
          <option disabled selected value="">Select a language.</option>
          <option
            v-for="language in languages"
            :key="language.code"
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
      <button type="submit" class="btn btn-primary btn-block">Translate</button>
    </form>
  </div>
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
    };
  },
  props: ["translatedEvent"],
  methods: {
    translateIt(e) {
      e.preventDefault();
      const encodedParams = new URLSearchParams();
      encodedParams.set("source_language", "auto");
      encodedParams.set("target_language", this.translateTo);
      encodedParams.set("text", this.toTranslateText);

      const options = {
        method: "POST",
        url: "https://text-translator2.p.rapidapi.com/translate",
        headers: {
          "content-type": "application/x-www-form-urlencoded",
          "X-RapidAPI-Key":
            "f13b8f2b1cmshec89c8fa4b56e25p14fe02jsnd6aaefb387bd",
          "X-RapidAPI-Host": "text-translator2.p.rapidapi.com",
        },
        data: encodedParams,
      };
      if (this.toTranslateText === "") {
        this.isValidText = true;
      } else {
        this.isValidText = false;
      }
      if (this.translateTo === "") {
        this.isValidLang = true;
      } else {
        this.isValidLang = false;
      }
      if (this.isValidLang || this.isValidText) {
        return;
      } else {
        axios
          .request(options)
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
      }
    },
  },
  created() {
    const options = {
      method: "GET",
      url: "https://text-translator2.p.rapidapi.com/getLanguages",
      headers: {
        "X-RapidAPI-Key": "f13b8f2b1cmshec89c8fa4b56e25p14fe02jsnd6aaefb387bd",
        "X-RapidAPI-Host": "text-translator2.p.rapidapi.com",
      },
    };
    axios
      .request(options)
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
</style>
