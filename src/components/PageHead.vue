<template>
  <div class="buttons-grid">
    <div id="area-cost">
      <PageHeadButton
        v-for="cost in costs"
        :key="cost.cost"
        :class="['button', buttonCostClass(cost.active)]"
        @click.native="buttonCost(cost)">
          {{cost.cost}}费
      </PageHeadButton>
    </div>

    <div class="area-team">
      <PageHeadButton
        @click.native="buttonAdd()"
        :class="['addteam', 'button', addTeamClass]">
          新增队伍
      </PageHeadButton>
      <PageHeadButton
        @click.native="buttonDelete()"
        :class="[deleteTeamClass, 'button']">
          删除队伍
      </PageHeadButton>
      <PageHeadButton
        v-for="team in teams"
        :key="team.name"
        @click.native="buttonTeam(team)"
        :class="['button', buttonTeamClass(getTeamActive(team.name))]">
          {{team.name}}
      </PageHeadButton>
    </div>

    <div class="area-change">
      <PageHeadButton
        class="button"
        @click.native="changeSeason">
          {{changeSeasonText}}
        </PageHeadButton>
    </div>

    <div class="area-clear">
      <PageHeadButton
        class="button"
        @click.native="buttonClear">
          {{buttonClearText}}
      </PageHeadButton>
    </div>

    <div :class="['area-population', 'population']">
      <div>人口：{{population}}</div>
    </div>
  </div>
</template>

<script>
// 总是死循环。解决：.slice().sort()
import PageHeadButton from './PageHeadButton.vue';

export default {
  name: 'PageHead',
  components: { PageHeadButton },
  props: {
    champions: Array,
    teams: Array,
    states: Object,
  },
  computed: {
    newTeams() {
      return this.teams.map((team) => ({
        name: team.name,
        active: this.listEqual(team.list, this.activeChampions),
      }));
    },
    // champions & population
    activeChampions() {
      return this.champions.filter((champion) => champion.active === true)
        .map((champion) => champion.name); // !又忘记了，champion是一个对象，不止有name
    },
    population() {
      return this.activeChampions.length;
    },
    existActiveChampion() {
      return this.population !== 0;
    },

    isStateTeam() {
      return this.newTeams.some((team) => team.active === true);
    },
    buttonClearText() {
      return this.population === 0 ? '全选' : '清空';
    },
    changeSeasonText() {
      return this.states.s3 ? 'to S2' : 'to S3';
    },

    // cost
    costs() {
      const championsCost = this.champions
        .map((champion) => champion.cost)
        .sort();
      const uniqueArray = (arr) => Array.from(new Set(arr));
      const uniqueChampionCost = uniqueArray(championsCost);
      const buttonActive = (cost) => this.champions
        .filter((champion) => champion.cost === cost)
        .some((champion) => champion.active === true);
      const costs = uniqueChampionCost.map((cost) => ({
        cost,
        active: buttonActive(cost) && !this.isStateTeam,
      }));
      return costs;
    },

    indexOfActiveTeam() {
      return this.newTeams.map((team) => team.active).indexOf(true);
    },

    nameOfActiveTeam() {
      return this.teams[this.indexOfActiveTeam].name;
    },

    deleteTeamClass() {
      return this.indexOfActiveTeam !== -1 ? 'active-delete' : 'inactive-delete';
    },

    // 以下4个 关于 add team
    isCorrectNumber() {
      return this.population >= 5 && this.population <= 11;
    },
    canAddTeam() {
      return this.isCorrectNumber && this.teams.length <= 12 && !this.isStateTeam;
    },
    addTeamClass() {
      return this.canAddTeam ? 'active-add' : 'inactive-add';
    },

  },

  methods: {
    getTeamActive(teamName) { //
      // console.log(this.newTeams.some((team) => team.name === teamName && team.active === true));
      // console.log(this.newTeams);
      return this.newTeams.some((team) => team.name === teamName && team.active === true);
    },
    listEqual(list1, list2) {
      return list1.slice().sort().toString() === list2.slice().sort().toString();
    },
    buttonDelete() {
      // 移除活跃的队伍
      const index = this.indexOfActiveTeam;
      if (index !== -1) {
        if (window.confirm(`删除队伍：${this.nameOfActiveTeam}`)) {
          this.teams.splice(index, 1);
        }
      }
    },
    buttonAdd() {
      if (!this.canAddTeam) {
        return;
      }
      // eslint-disable-next-line
      const name = window.prompt('请输入队伍名字', `自定义${this.teams.length + 1}`);
      if (name === null) { // 应该进行更多检查
        return;
      }
      if (this.teams.map((team) => team.name).includes(name)) {
        // eslint-disable-next-line
        window.alert('名字重复！'); // 应该在自定义框中完成
        return;
      }
      const newTeam = {
        list: this.activeChampions,
        name,
      };
      this.teams.push(newTeam);
    },
    buttonCostClass(isActive) {
      return isActive ? 'active-cost' : 'inactive-cost';
    },
    buttonTeamClass(isActive) {
      return isActive ? 'active-team' : 'inactive-team';
    },
    changeSeason() {
      this.states.s3 = !this.states.s3;
    },
    // button helper
    allShow() {
      this.champions.forEach((champion) => (champion.active = true));
    },
    clear() {
      this.champions.forEach((champion) => (champion.active = false));
    },

    // button
    buttonClear() {
      if (this.existActiveChampion) {
        this.clear();
      } else {
        this.allShow();
      }
    },

    buttonCost(cost) {
      if (this.isStateTeam) {
        this.clear();
      }

      if ( // 存在该cost的英雄 --> 全false
        this.champions.some(
          (champion) => champion.cost === cost.cost && champion.active === true,
        )
      ) {
        this.champions.forEach((champion) => {
          if (champion.cost === cost.cost) {
            champion.active = false;
          }
        });
      } else { // 不存在该cost的英雄 --> 全true
        this.champions.forEach((champion) => {
          if (champion.cost === cost.cost) {
            champion.active = true;
          }
        });
      }
    },

    buttonTeam(team) {
      // 有意做成非响应式 发现很多bug还是响应式
      if (!this.listEqual(team.list, this.activeChampions)) {
        // 如果当前显示的不是该队 高亮英雄
        this.champions.forEach((champion) => {
          if (team.list.includes(champion.name)) {
            champion.active = true;
          } else {
            champion.active = false;
          }
        });
      } else {
        // 如果当前显示的是该队 亮所有
        this.allShow();
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
    ~"/" 110px 800px; // 需要转义
  gap: 4px 25px;

  @media screen and (max-width: 1000px) {
    margin-top: 15px;
    justify-content: left;
    grid-template:
      "a b" 35px
      "c d" 35px
      "e d" 35px
      ~"/" 80px 600px; // 需要转义
    gap: 3px 10px;
  }
}

.area-cost {
  grid-area: b;
}
.area-team {
  grid-area: d;
  display: grid;
  grid-template-areas: "a"
                       "a";
  grid-auto-flow: column dense;
  grid-auto-columns: 112px;
  // gap: 2px 2px;
  @media screen and (max-width: 1000px) {
    grid-auto-columns: 90px;
  }
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

</style>
