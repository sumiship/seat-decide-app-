<template>
  <div id="app">
    <Seats :people="people" :selectMember="selectMember" />
    <div class="control">
      <Names :names="names" />
      <div class="groups">
        <div class="search-box">
          <div @click="search()" class="search">
            search
          </div>
          <!-- <input
            type="number"
            v-model="testTimes"
            placeholder="試行回数(0六個推奨)"
          /> -->
          <div>
            <input type="radio" id="light" value="10000" v-model="testTimes" />
            <label for="light">light</label>
          </div>
          <div>
            <input type="radio" id="nomal" value="100000" v-model="testTimes" />
            <label for="nomal">nomal</label>
          </div>
          <div>
            <input
              type="radio"
              id="heavy"
              value="1000000"
              v-model="testTimes"
            />
            <label for="heavy">heavy</label>
          </div>
        </div>
        <div
          class="group"
          v-for="(group, index) in groups"
          :key="index"
          @mouseover="highlight(group.member)"
          @mouseleave="unhighlight()"
        >
          <div class="group__name">{{ group.name }}</div>
          <div class="group__weight">重要度：{{ group.weight }}</div>
          <div class="group__member">
            <span
              v-for="(member, memberIndex) in group.member"
              :key="memberIndex"
            >
              {{ names[member - 1] }}
            </span>
          </div>
          <div @click.stop="group_delete(index)" class="group__delete">
            delete
          </div>
        </div>
        <div class="new-group">
          <input type="text" v-model="group_name" placeholder="group name" />
          <!-- <input
            type="text"
            v-model="group_weight"
            placeholder="group weight"
          /> -->
          <br />
          <label for="volume">重要度</label><br />
          <input
            type="range"
            id="volume"
            name="volume"
            min="0"
            max="50"
            v-model="group_weight"
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
  private groups: GroupData[] = [];
  private selectMember: number[] = [];
  private group_name = "";
  private group_weight = "25";
  private group_member = "";
  private testTimes = "100000";
  private names = [];
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

  private new_group(): void {
    if (this.group_member == "") {
      alert("メンバーを入れて");
      return;
    }
    const saveData = {
      name: this.group_name,
      weight: Number(this.group_weight),
      member: this.string2arr(this.group_member),
    };
    this.group_name = this.group_member = "";
    this.group_weight = "25";
    this.groups.push(saveData);
    localStorage.groups = JSON.stringify(this.groups);
  }
  private group_delete(id: number): void {
    this.groups.splice(id, 1);
    this.selectMember = [];
    localStorage.groups = JSON.stringify(this.groups);
  }

  private array_ave(arr: number[]): number {
    return arr.reduce((previous, current) => previous + current) / arr.length;
  }
  private array_variance(arr: number[]): number {
    return (
      this.array_ave(arr.map((current) => current ** 2)) -
      this.array_ave(arr) ** 2
    );
  }
  private array_varianceVec2(arr: number[][]): number {
    let x: number[] = [];
    let y: number[] = [];
    arr.forEach((data) => {
      x.push(data[0]);
      y.push(data[1]);
    });
    return this.array_variance(x) + this.array_variance(y);
  }
  private array_evaluation(groupData: GroupData, resource: number[][]): number {
    let seatList: number[][] = [];
    groupData.member.forEach((data) => {
      seatList.push(resource[data - 1]);
    });
    return (
      (Math.floor(this.array_varianceVec2(seatList) * 100000) / 100000) *
      groupData.weight
    );
  }
  private array_shuffle(arr: number[][]): void {
    for (let i = arr.length; 1 < i; i--) {
      const key = Math.floor(Math.random() * i);
      [arr[key], arr[i - 1]] = [arr[i - 1], arr[key]];
    }
  }

  private search(): void {
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
    localStorage.people = JSON.stringify(this.people);
  }

  private highlight(member: number[]): void {
    this.selectMember = member;
  }
  private unhighlight(): void {
    this.selectMember = [];
  }

  mounted(): void {
    this.names = JSON.parse(localStorage.names);
    if (localStorage.groups) this.groups = JSON.parse(localStorage.groups);
    if (localStorage.people) this.people = JSON.parse(localStorage.people);
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
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
  align-items: flex-start;
  font-size: 2rem;
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
.search-box input {
  width: 27px;
  height: 27px;
  margin-right: 4px;
}
.search {
  width: 100px;
  padding: 8px 0;
  background-color: darkturquoise;
  color: white;
  border-radius: 10px;
  cursor: pointer;
  line-height: 30px;
}
.search:hover {
  background-color: rgb(158, 217, 218);
  box-shadow: 0 0 3px 3px darkturquoise;
}
.group {
  padding: 10px;
  border: 2px solid rgb(223, 160, 160);
  border-radius: 10px;
  /* height: 150px; */
  margin: 10px;
  font-size: 1.4rem;
}
.group:hover {
  box-shadow: 0 0 2px 2px red;
  /* cursor: pointer; */
}
.group div:nth-child(n) {
  margin-bottom: 8px;
}
.group__delete {
  width: 80px;
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
  /* height: 170px; */
  border-radius: 10px;
  padding: 10px;
  margin: 10px;
  font-size: 1.4rem;
}
.new-group input {
  margin-bottom: 10px;
}
.new-gropu__add {
  color: white;
  background-color: cornflowerblue;
  width: 70px;
  border-radius: 8px;
  margin: 0 auto;
  cursor: pointer;
}
.new-gropu__add:hover {
  background-color: rgb(151, 173, 214);
  box-shadow: 0 0 3px 3px cornflowerblue;
}
</style>
