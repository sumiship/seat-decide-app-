<template>
  <div class="names">
    <div class="name" v-for="(name, index) in names" :key="index">
      <span class="name_id">{{ index + 1 }}:</span
      ><input type="text" v-model="names[index]" />
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Watch, Emit } from "vue-property-decorator";

@Component({})
export default class App extends Vue {
  private names = [
    "太郎",
    "二郎",
    "三郎",
    "サブちゃん",
    "ニノ助",
    "太一",
    "じい",
    "米たろう",
    "米山",
    "米介",
    "米吉",
    "SONY",
    "ごめ",
    "北小島",
    "大関",
    "春の富士",
    "森鴎外",
    "ヒュー",
    "ベース",
    "明日",
    "晴太郎",
    "曇り太郎",
    "曇天太郎",
    "雲泥太郎",
  ];

  @Watch("names")
  @Emit("names")
  updateNames(): void {
    localStorage.names = JSON.stringify(this.names);
  }

  mounted(): void {
    if (!localStorage.names) localStorage.names = JSON.stringify(this.names);
    this.names = JSON.parse(localStorage.names);
  }
}
</script>
<style scoped>
.names {
  width: 20%;
  background-color: beige;
  text-align: start;
}
.name {
  font-size: 3rem;
}
.name_id {
  width: 15%;
  display: inline-block;
}
.name input {
  font-size: 2rem;
  width: 80%;
}
</style>
