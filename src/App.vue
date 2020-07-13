<template>
  <div id="app">
    <MyPage
      v-if="states.s3"
      :champions="championsS3"
      :classes="classesS3"
      :origins="originsS3"
      :teams="teamsS3"
      :state="states"
      img-folder="S3/"
    />
    <MyPage
      v-else
      :champions="championsS2"
      :classes="classesS2"
      :origins="originsS2"
      :teams="teamsS2"
      :state="states"
      img-folder="S2/"
    />
  </div>
</template>

<script>
// button 响应式简单点 我佛了
// 需要自定义prompt alert
// hover th button 不太实用
//
// span滑动动画
// 服务端渲染
// @click.native
import MyPage from './components/MyPage.vue';

export default {
  name: 'App',
  components: {
    MyPage,
  },
  data() {
    return {
      championsS2: [],
      championsS3: [],
      teamsS2: [],
      teamsS3: [],
      originsS2: [],
      originsS3: [],
      classesS2: [],
      classesS3: [],
      states: {
        s3: true,
      },
    };
  },
  methods: {
    saveTeamToLocal() {
      localStorage.teamsS2 = JSON.stringify(this.teamsS2);
      localStorage.teamsS3 = JSON.stringify(this.teamsS3);
      // console.log('存入localStorage!');
    },
  },
  created() {
    window.addEventListener('beforeunload', () => this.saveTeamToLocal());
    const hostname = 'http://39.105.135.195';
    // const hostname = 'http://127.0.0.1:8080';
    fetch(`${hostname}/data/championsS2`)
      .then((res) => res.json())
      .then((res) => (this.championsS2 = res));
    fetch(`${hostname}/data/championsS3`)
      .then((res) => res.json())
      .then((res) => (this.championsS3 = res));
    if (localStorage.getItem('teamsS2')) {
      // console.log('存在teamsS2');
      this.teamsS2 = JSON.parse(localStorage.teamsS2);
    } else {
      // console.log('不存在teamsS2');
      fetch(`${hostname}/data/teamsS2`)
        .then((res) => res.json())
        .then((res) => { this.teamsS2 = res; });
    }
    if (localStorage.getItem('teamsS3')) {
      // console.log('存在teamsS3');
      this.teamsS3 = JSON.parse(localStorage.teamsS3);
    } else {
      // console.log('不存在teamsS3');
      fetch(`${hostname}/data/teamsS3`)
        .then((res) => res.json())
        .then((res) => { this.teamsS3 = res; });
    }

    fetch(`${hostname}/data/originsS2`)
      .then((res) => res.json())
      .then((res) => (this.originsS2 = res));
    fetch(`${hostname}/data/originsS3`)
      .then((res) => res.json())
      .then((res) => (this.originsS3 = res));

    fetch(`${hostname}/data/classesS2`)
      .then((res) => res.json())
      .then((res) => (this.classesS2 = res));
    fetch(`${hostname}/data/classesS3`)
      .then((res) => res.json())
      .then((res) => (this.classesS3 = res));
  },
  // beforeMount() {
  //   this.teamsS2.forEach((team) => (team.active = false)); // 为何team有用，champion没用
  //   this.teamsS3.forEach((team) => (team.active = false));
  // },
  destroyed() {
    window.removeEventListener('beforeunload', () => this.saveTeamToLocal());
  },
};
</script>
