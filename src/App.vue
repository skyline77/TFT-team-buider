<template>
  <div id="app">
    <MyPage
      v-if="states.s3"
      :champions="championsS3"
      :classes="classesS3"
      :origins="originsS3"
      :teams="teamsS3"
      :state="states"
      imgfolder="picture_s3/"
    />
    <MyPage
      v-else
      :champions="championsS2"
      :classes="classesS2"
      :origins="originsS2"
      :teams="teamsS2"
      :state="states"
      imgfolder="picture_s2/"
    />
  </div>
</template>

<script>
// 2 github
// 3 localStorage
// vue 风格指南
// 不用class show，用compute啊 然后改img的active 算了能用就行
//
// span滑动动画
// 服务端渲染
// @click.native
// h2数据库
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
  created() {
    const hostname = 'http://39.105.135.195/';
    fetch(`${hostname}/data/championsS2`)
      .then((res) => res.json())
      .then((res) => (this.championsS2 = res));
    fetch(`${hostname}/data/championsS3`)
      .then((res) => res.json())
      .then((res) => (this.championsS3 = res));
    fetch(`${hostname}/data/teamsS2`)
      .then((res) => res.json())
      .then((res) => (this.teamsS2 = res));
    fetch(`${hostname}/data/teamsS3`)
      .then((res) => res.json())
      .then((res) => (this.teamsS3 = res));

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
};
</script>
