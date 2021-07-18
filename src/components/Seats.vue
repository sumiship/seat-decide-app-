<template>
  <div class="seats">
    <div class="table1"></div>
    <div
      class="row-seats"
      v-for="(row_seats, rowIndex) in seatArr"
      :key="rowIndex"
    >
      <div class="seat" v-for="(seat, colIndex) in row_seats" :key="colIndex">
        {{ seat }}
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
  private people!: [];

  @Watch("people")
  updatesort() {
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

  mounted() {
    // console.log(this.people);
    this.seatArr = this.sort();
    // console.log(this.seatArr);
  }
}
</script>

<style>
.seats {
  width: 60%;
  margin: 0 auto;
  border-right: 20px rgb(100, 98, 98) solid;
  border-left: 20px rgb(100, 98, 98) solid;
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
