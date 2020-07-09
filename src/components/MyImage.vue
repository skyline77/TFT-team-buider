<template>
  <picture>
    <source :srcset="require('@/assets/' + imgfolder + champion.name + '.webp')"
            type="image/webp" />
    <img
      :src="require('@/assets/' + imgfolder + champion.name + '.png')"
      :alt="champion.name"
      :class="['cost-' + champion.cost, champion.classes, 'count-' + count, 'img']"
    />
  </picture>
</template>

<script>
export default {
  name: 'MyImage',
  props: {
    imgfolder: String,
    champion: Object,
    count: Number,
  },
};
</script>

<style lang="less" scoped>
@width-desk-count1: 27px;
@width-desk-count2: 24px;
@width-desk-count3: 22px;
@width-mobi-count1: 19px;
@width-mobi-count2: 18px;
@width-mobi-count3: 16px;
@width-mobi-desk: 1000px;

//basic: 在页面中不会改变
.img-layout() {
  margin: 2px 3px;
  border: solid 3px;
  border-radius: 2px;
  @media screen and (max-width: @width-mobi-desk) {
    margin: 2px 2px;
    border-width: 2px;
    border-radius: 1px;
  }
}

.img-state() { // 应该单独 show 和 noshow
  opacity: 0.15;
  filter: alpha(opacity=15);
  &:hover {
    cursor: pointer;
  }
  &.show {
    opacity: 1;
    filter: alpha(opacity=100);
  }
}

.img-animation() {
  transition: opacity 0.1s;
}

.img {
  .img-layout();
  .img-state();
  .img-animation();
}

.cost- {
  &1{
    border-color: hsl(208, 10%, 66%);
  }
  &2{
    border-color: hsl(144, 75%, 30%);
  }
  &3{
    border-color: hsl(206, 75%, 49%);
  }
  &4{
    border-color: hsl(316, 55%, 45%);
  }
  &5{
    border-color: hsl(38, 85%, 60%);
  }
  &7{
    border-color: hsl(285, 25%, 68%);
  }
}

.count- {
  &1 {
    width: @width-desk-count1;
    height: @width-desk-count1;
    @media screen and (max-width: @width-mobi-desk) {
      width: @width-mobi-count1;
      height: @width-mobi-count1;
    }
  }
  &2 {
    width: @width-desk-count2;
    height: @width-desk-count2;
    @media screen and (max-width: @width-mobi-desk) {
      width: @width-mobi-count2;
      height: @width-mobi-count2;
    }
  }
  &3 {
    width: @width-desk-count3;
    height: @width-desk-count3;
    @media screen and (max-width: @width-mobi-desk) {
      width: @width-mobi-count3;
      height: @width-mobi-count3;
    }
  }
}
</style>
