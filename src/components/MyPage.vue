<template>
  <div>
    <MyButton :champions="champions" :teams="teams" :states="state" />
    <MyTable :champions="champions" :origins="origins" :classes="classes" :imgfolder="imgfolder" />
  </div>
</template>

<script>
import MyButton from './MyButton.vue';
import MyTable from './MyTable.vue';

export default {
  name: 'MyPage',
  components: { MyButton, MyTable },
  props: {
    champions: Array,
    classes: Array,
    origins: Array,
    teams: Array,
    imgfolder: String,
    state: Object,
  },
};
</script>
