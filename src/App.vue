<template>
  <div id="app">
    <Seats :people="people" />
    <div class="control">
      <Names />
      <div class="groups">
        <div class="search-box">
          <div @click="search()" class="search">
            search
          </div>
          <input
            type="number"
            v-model="testTimes"
            placeholder="試行回数(0六個推奨)"
          />
        </div>
        <div class="group" v-for="(group, index) in groups" :key="index">
          <div class="group__name">{{ group.name }}</div>
          <div class="group__weight">重要度：{{ group.weight }}</div>
          <div class="group__member">{{ group.member }}</div>
          <div @click="group_delete(index)" class="group__delete">delete</div>
        </div>
        <div class="new-group">
          <input type="text" v-model="group_name" placeholder="group name" />
          <input
            type="text"
            v-model="group_weight"
            placeholder="group weight"
          />
          <input
            type="text"
            v-model="group_member"
            placeholder="group member ex)1,2,3"
          />
          <div @click="new_group()" class="new-gropu__add">add</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Seats from "./components/Seats.vue";
import Names from "./components/Names.vue";

interface Person {
  person_id: number;
  position: number[];
}
interface People {
  person: Person[];
}
interface GroupData {
  name: string;
  weight: number;
  member: number[];
}

@Component({
  components: {
    Seats,
    Names,
  },
})
export default class App extends Vue {
  private groups: any[] = [];
  private group_name = "";
  private group_weight = "";
  private group_member = "";
  private testTimes = "";
  private people: number[][] = [
    [1, 1],
    [1, 2],
    [1, 3],
    [1, 4],
    [2, 1],
    [2, 2],
    [2, 3],
    [2, 4],
    [3, 1],
    [3, 2],
    [3, 3],
    [3, 4],
    [4, 1],
    [4, 2],
    [4, 3],
    [4, 4],
    [5, 1],
    [5, 2],
    [5, 3],
    [5, 4],
    [6, 1],
    [6, 2],
    [6, 3],
    [6, 4],
  ];
  string2arr(string: string): number[] {
    let retArr = [];
    let num = 0;
    let i = 0;
    while (i < string.length) {
      const item = string.slice(i++, i);
      if (item == ",") {
        retArr.push(num);
        num = 0;
      } else if (num == 0) {
        num = Number(item);
      } else {
        num = num * 10 + Number(item);
      }
    }
    if (num != 0) retArr.push(num);
    return retArr;
  }

  new_group(): void {
    const saveData = {
      name: this.group_name,
      weight: Number(this.group_weight),
      member: this.string2arr(this.group_member),
    };
    this.group_name = this.group_weight = this.group_member = "";
    this.groups.push(saveData);
  }
  group_delete(id: number): void {
    this.groups.splice(id, 1);
  }

  array_ave(arr: number[]): number {
    return arr.reduce((previous, current) => previous + current) / arr.length;
  }
  array_variance(arr: number[]): number {
    return (
      this.array_ave(arr.map((current) => current ** 2)) -
      this.array_ave(arr) ** 2
    );
  }
  array_varianceVec2(arr: number[][]): number {
    let x: number[] = [];
    let y: number[] = [];
    arr.forEach((data) => {
      x.push(data[0]);
      y.push(data[1]);
    });
    return this.array_variance(x) + this.array_variance(y);
  }
  array_evaluation(groupData: GroupData, resource: number[][]): number {
    let seatList: number[][] = [];
    groupData.member.forEach((data) => {
      seatList.push(resource[data - 1]);
    });
    // console.log("----------------------");
    // console.log(groupData);
    // console.log(JSON.parse(JSON.stringify(resource)));
    // console.log(this.array_varianceVec2(seatList) * groupData.weight);
    return this.array_varianceVec2(seatList) * groupData.weight;
  }
  array_shuffle(arr: number[][]): void {
    for (let i = arr.length; 1 < i; i--) {
      const key = Math.floor(Math.random() * i);
      [arr[key], arr[i - 1]] = [arr[i - 1], arr[key]];
    }
  }

  search(): void {
    let best = -4200000000000000;
    const resource = JSON.parse(JSON.stringify(this.people));
    [...Array(Number(this.testTimes))].forEach(() => {
      let score = 0;
      this.array_shuffle(resource);
      this.groups.forEach((groupData) => {
        if (score < best) return;
        score -= this.array_evaluation(groupData, resource);
      });
      if (score > best) {
        best = score;
        this.people = JSON.parse(JSON.stringify(resource));
      }
    });
    // console.log(this.people);
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.control {
  display: flex;
}
.groups {
  display: flex;
  flex-wrap: wrap;
  /* justify-content: flex-start; */
  align-items: flex-start;
  width: 80%;
}
.search-box {
  width: 100%;
  height: 60px;
  padding: 7px 0;
  display: flex;
  justify-content: space-around;
}
.search {
  width: 100px;
  padding: 8px 0;
  background-color: darkturquoise;
  color: white;
  border-radius: 10px;
  cursor: pointer;
}
.search:hover {
  background-color: rgb(158, 217, 218);
  box-shadow: 0 0 3px 3px darkturquoise;
}
.group {
  padding: 10px;
  border: 2px solid rgb(223, 160, 160);
  border-radius: 10px;
  height: 150px;
  margin: 10px;
}
.group div:nth-child(n) {
  margin-bottom: 8px;
}
.group__delete {
  width: 60px;
  color: white;
  background-color: rgb(235, 63, 63);
  margin: 0 auto;
  border-radius: 8px;
  cursor: pointer;
}
.group__delete:hover {
  background-color: rgb(241, 175, 175);
  box-shadow: 0 0 3px 3px rgb(235, 63, 63);
}
.new-group {
  width: 200px;
  border: 3px solid rgb(182, 177, 177);
  height: 150px;
  border-radius: 10px;
  padding: 10px;
  margin: 10px;
}
.new-group input {
  margin-bottom: 10px;
}
.new-gropu__add {
  color: white;
  background-color: cornflowerblue;
  width: 50px;
  border-radius: 8px;
  margin: 0 auto;
  cursor: pointer;
}
.new-gropu__add:hover {
  background-color: rgb(151, 173, 214);
  box-shadow: 0 0 3px 3px cornflowerblue;
}
</style>
