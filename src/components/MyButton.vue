<template>
  <div class="buttons-grid">
    <div id="area-cost">
      <button
        v-for="cost in costs"
        :key="cost.cost"
        :class="['button', cost.classes]"
        @click="buttonCost(cost)"
        type="button"
      >{{cost.cost}}费</button>
    </div>

    <div class="area-team">
      <button
        type="button"
        v-for="team in teams"
        :key="team.name"
        @click="buttonTeam(team)"
        :class="['button', team.classes]"
      >{{team.name}}</button>
    </div>

    <div class="area-change" @click="changeSeason">
      <button type="button" class="button">{{changeSeasonText}}</button>
    </div>

    <div class="area-clear">
      <button type="button" class="button" @click="buttonClear">{{buttonClearText}}</button>
    </div>

    <div :class="['area-population', 'population']">
      <div>人口：{{population}}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MyButton',
  props: {
    champions: Array,
    teams: Array,
    states: Object,
  },
  data() {
    return {
      state: {
        stateTeam: false,
      },
    };
  },
  computed: {
    population() {
      // 应该是computed？
      return this.champions.filter((champion) => champion.classes.show === true)
        .length;
    },
    buttonClearText() {
      if (this.population === 0) {
        return '全选';
      }
      return '清空';
    },
    changeSeasonText() {
      if (this.states.s3) {
        return 'to S2';
      }
      return 'to S3';
    },

    costs() {
      const championsCost = this.champions
        .map((champion) => champion.cost)
        .sort();
      const uniqueArray = (arr) => Array.from(new Set(arr));
      const uniqueChampionCost = uniqueArray(championsCost);
      const buttonActive = (cost) => this.champions
        .filter((champion) => champion.cost === cost)
        .some((champion) => champion.classes.show === true);
      const costs = uniqueChampionCost.map((cost) => ({
        cost,
        classes: {
          button_active: buttonActive(cost) && !this.state.stateTeam,
          // button_guide_active: buttonActive(cost) && this.state.stateTeam,
        },
      }));
      return costs;
    },
  },

  methods: {
    changeSeason() {
      this.states.s3 = !this.states.s3;
    },
    // button helper
    allShow() {
      this.champions.forEach((champion) => (champion.classes.show = true));
      this.teams.forEach(
        (teamObject) => (teamObject.classes.button_active = false),
      );
    },
    clear() {
      this.champions.forEach((champion) => (champion.classes.show = false));
      this.teams.forEach(
        (teamObject) => (teamObject.classes.button_active = false),
      );
    },

    // button
    buttonClear() {
      if (this.champions.some((champion) => champion.classes.show === true)) {
        this.clear();
      } else {
        this.allShow(); // $options.methods.nmd
      }

      this.state.stateTeam = false;
    },

    buttonCost(cost) {
      if (this.state.stateTeam) {
        this.clear();
      }

      if (
        this.champions.some(
          (champion) => champion.cost === cost.cost && champion.classes.show === true,
        )
      ) {
        this.champions.forEach((champion) => {
          // 存在cost的英雄 --> 全false
          if (champion.cost === cost.cost) {
            champion.classes.show = false;
          }
        });
      } else {
        // 不存在cost的英雄 --> 全true
        this.champions.forEach((champion) => {
          if (champion.cost === cost.cost) {
            champion.classes.show = true;
          }
        });
      }

      this.state.stateTeam = false;
    },

    buttonTeam(team) {
      const listEqual = (listA, listB) => listA.sort().toString() === listB.sort().toString();

      if (
        !listEqual(
          team.list,
          this.champions
            .filter((champion) => champion.classes.show === true)
            .map((champion) => champion.name),
        )
      ) {
        // 如果当前显示的不是该队

        // 高亮英雄
        this.champions.forEach((champion) => {
          if (team.list.includes(champion.name)) {
            champion.classes.show = true;
          } else {
            champion.classes.show = false;
          }
        });

        // 高亮button team
        this.teams.forEach((teamObject) => {
          // 这样才行？之前用listEqual不行 teamObject.list === team.list
          if (teamObject.list === team.list) {
            teamObject.classes.button_active = true;
          } else {
            teamObject.classes.button_active = false;
          }
        });

        this.state.stateTeam = true;
      } else {
        // 如果当前显示的是该队
        // 高亮英雄
        this.allShow();
        this.state.stateTeam = false;

        // 高亮button team
        this.teams.forEach((teamObject) => {
          teamObject.classes.button_active = false;
        });
      }
    },
  },
};
</script>

<style lang="less" scoped>
.buttons-grid {
  display: grid;
  justify-content: center;
  align-items: flex-start;
  grid-template:
    "a b" 35px
    "c d" 35px
    "e d" 35px
    ~"/" 110px 700px; // 需要转义
  gap: 2px 25px;

  @media screen and (max-width: 1000px) {
    margin-top: 15px;
    justify-content: left;
    grid-template:
      "a b" 35px
      "c d" 35px
      "e d" 35px
      ~"/" 80px 550px; // 需要转义
    gap: 2px 10px;
  }
}

.area-cost {
  grid-area: b;
}
.area-team {
  grid-area: d;
}

.area-change {
  grid-area: a;
}

.area-clear {
  grid-area: c;
}

.area-population {
  grid-area: e;
  align-self: center;
}

.population {
    font-weight: bold;
    font-size: larger;
    color: hsl(206, 45%, 45%);
    padding-left: 10px;
  @media screen and (max-width: 1000px) {
    font-size: 10pt;
  }
}

.button {
  .button-font();
  .button-layout();
  .button-style();
  .button-animation();
}

.button_active.button { // 也应该定义 active nonactive
    color: hsl(208, 65%, 45%);
    border-color: hsl(208, 65%, 45%);
}

.button-font(){
  font-size: 10pt;
  @media screen and (max-width: 1000px) {
    font-size: 8pt;
  }
}

.button-layout(){
  margin: 4px 6px;
  padding: 0 8px;
  height: 30px;
  min-width: 100px;
  border: solid 1px;
  border-radius: 5px;
  &:focus {
    outline: 0;
  }
  &:hover {
    cursor: pointer;
  }
  @media screen and (max-width: 1000px) {
    margin: 2px 5px;
    padding: 2 8px;
    height: 30px;
    min-width: 60px;
  }
}

.button-style(){
  background-color: white;
  color: hsl(206, 0%, 40%);
  border-color: hsl(208, 0%, 60%);
  box-shadow: 0.5px 0.5px;
  &:active,
  &:hover {
    border-color: hsl(206, 45%, 45%);
  }
}

.button-animation(){
  transition: color 0.1s;
  transition: border-color 0.1s;
}
</style>
