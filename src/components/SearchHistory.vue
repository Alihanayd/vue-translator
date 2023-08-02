<template>
  <div class="well" v-if="history.length > 0">
    <h3>Search History</h3>
    <ul class="list-group text-left">
      <li
        class="list-group-item"
        :key="item.translatedText"
        v-for="item in history"
      >
        <div>
          <strong>{{ item.from }} => {{ item.to }}</strong>
        </div>
        <br />
        <div>
          <strong class="text-success">{{ item.text }}</strong> ->
          <span class="text-warning">{{ item.translatedText }}</span>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: ["history"],
};
</script>

<style scoped></style>
