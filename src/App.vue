<template>
  <div id="app">
    <date-picker v-model="value" />
  </div>
</template>

<script>
import DatePicker from "./components/DatePicker.vue";

export default {
  name: "App",
  components: {
    DatePicker,
  },
  data() {
    return {
      value: new Date(),
    };
  },
};
</script>

<style>
#app {
  color: #2c3e50;
  margin-top: 60px;
}
</style>
