<template>
  <div class="well" v-if="history.length > 0">
    <h3>Search History</h3>
    <ul class="list-group text-left">
      <li
        class="list-group-item border"
        :key="index"
        v-for="(item, index) in history"
      >
        <div>
          <strong class="text-white"
            >{{ item.from }}
            <i class="fa fa-chevron-right mx-2 text-white fs-5"></i>
            {{ item.to }}</strong
          >
        </div>
        <br />
        <div>
          <strong class="text-success">{{ item.text }}</strong>
          <i class="fa fa-chevron-right mx-2 text-white fs-5"></i>
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
