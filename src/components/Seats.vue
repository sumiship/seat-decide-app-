<template>
  <div class="seats">
    <div class="table1"></div>
    <div
      class="row-seats"
      v-for="(row_seats, rowIndex) in seatArr"
      :key="rowIndex"
    >
      <div
        class="seat"
        :style="select(seat)"
        v-for="(seat, colIndex) in row_seats"
        :key="colIndex"
      >
        {{ names[seat - 1] }}
      </div>
      <div :class="'table' + (rowIndex % 2)"></div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop, Watch } from "vue-property-decorator";

// interface Person {
//   person_id: number;
//   position: number[];
// }
// interface People {
//   person: Person[];
// }

@Component({})
export default class App extends Vue {
  @Prop()
  selectMember!: number[];

  @Prop()
  private people!: [];
  private names: string[] = [
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

  @Watch("people")
  updatesort(): void {
    this.seatArr = this.sort();
    // console.log(this.)
  }

  private seatArr: number[][] = [];

  private seatbox = [4, 6];

  private sort() {
    let retArr = [
      [0, 0, 0, 0],
      [0, 0, 0, 0],
      [0, 0, 0, 0],
      [0, 0, 0, 0],
      [0, 0, 0, 0],
      [0, 0, 0, 0],
    ];
    this.people.forEach((person: number[], index: number) => {
      // console.log(person);
      // console.log(person);
      // console.log(index);
      retArr[person[0] - 1][person[1] - 1] = index + 1;
      // console.log(retArr);
    });
    return retArr;
  }

  @Watch("selectMember")
  private select(person: number): string {
    // console.log(person);
    if (this.selectMember.includes(person))
      return "background-color: rgb(236, 169, 189);";
    return "";
  }

  mounted() {
    if (localStorage.names) this.names = JSON.parse(localStorage.names);
    this.seatArr = this.sort();
    // console.log(this.selectMember);
  }
}
</script>

<style>
.seats {
  width: 60%;
  margin: 0 auto;
  border-right: 20px rgb(100, 98, 98) solid;
  border-left: 20px rgb(100, 98, 98) solid;
  /* background-color: rgb(236, 169, 189); */
  font-size: 2rem;
}
.row-seats {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
.seat {
  width: 25%;
}
.table1 {
  height: 20px;
  width: 100%;
  background-color: rgb(100, 98, 98);
}
.table0 {
  height: 20px;
  width: 100%;
  background-color: rgb(230, 167, 109);
}
</style>
