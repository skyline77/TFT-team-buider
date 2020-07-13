<template>
  <div>
    <PageHead :champions="champions"
              :teams="teams"
              :states="state" />
    <PageTable :champions="champions"
               :origins="origins"
               :classes="classes"
               :img-folder="imgFolder" />
  </div>
</template>

<script>
import PageHead from './PageHead.vue';
import PageTable from './PageTable.vue';

export default {
  name: 'MyPage',
  components: { PageHead, PageTable },
  props: {
    champions: Array,
    classes: Array,
    origins: Array,
    teams: Array,
    imgFolder: String,
    state: Object,
  },
};
</script>
