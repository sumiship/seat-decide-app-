<template>
  <div class="names">
    <div class="name" v-for="(name, index) in names" :key="index">
      {{ index + 1 }}: <input type="text" v-model="names[index]" />
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({})
export default class App extends Vue {
  private names = [
    "taro",
    "jiro",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
    "",
  ];
}
</script>
<style scoped>
.names {
  width: 20%;
  background-color: beige;
  text-align: start;
}
</style>
