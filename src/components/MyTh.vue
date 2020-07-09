<template>
  <th class="th">
    <div :class="['th-text', textActive]">{{text}}</div>
    <div class="left-span-div">
      <span
        v-for="(num,index) in numbers"
        :key="num"
        :class="['span left-span', leftSpanActive(index)]"
      >{{ num }}</span>
      <span :class="['span', 'right-span', rightSpanActive]">{{currentNumber}}</span>
    </div>
  </th>
</template>

<script>
export default {
  name: 'MyTh',
  props: {
    text: String,
    numbers: Array,
    whichSpanActive: Number,
    currentNumber: Number,
    isActive: Boolean,
  },
  computed: {
    textActive() {
      return this.isActive === true ? 'text-active' : 'text-inactive';
    },
    rightSpanActive() {
      return this.isActive === true ? 'right-span-active' : 'right-span-inactive';
    },
  },
  methods: {
    leftSpanActive(index) {
      return this.whichSpanActive === index ? 'left-span-active' : 'left-span-inactive';
    },
  },
};
</script>

<style lang="less" scoped>
// 理想状态下，th将active通过props传递给span和div
// 直接用后代选择器算了
// 也不用，MyTh作为最高层传入，th span 同级
// 但会有两个active，需要区分
// 基础样式，如果很多需要按照layout style分解；
// 附加样式，根据状态分解
// th ---|-- text
//       |-- left-span
//       |-- right-span
.th {
  padding: 0 8px 0 8px;
  min-width: 70px;
  transition: color 0.1s;
  @media screen and (max-width: 1000px) {
    padding: 0 5px 0 5px;
    min-width: 50px;
  }
  &.th-3 { // 使th比数字栏宽，防止换行
    min-width: 74px;
  }
  &.th-4 {
    min-width: 90px;
  }
}

.th-text {
  padding: 10px 0 5px 0;
  font-size: 11pt;
  @media screen and (max-width: 1000px) {
    padding: 8px 0 1px 0;
    font-size: 8pt;
  }
}

.text-active {
  color: hsl(206, 45%, 45%);
}

.text-inactive {
  color: hsl(1, 0%, 80%);
}

.span {
  display: inline-block;
  padding: 0 0 5px 0;
  font-size: 11pt;
  transition: color 0.1s;
  @media screen and (max-width: 1000px) {
    font-size: 9pt;
    padding: 0 0 0 0;
  }
}

.left-span {
  padding: 0 4px;
  border-right: solid 1px hsl(1, 0%, 92%);
  // height: 13pt;
  &:last-child {
    border-right: none;
  }
  @media screen and (max-width: 1000px) {
    padding-left: 0;
  }
}

.left-span-active {
  font-size: 13pt;
  color: hsl(32, 100%, 68%);
  @media screen and (max-width: 1000px) {
    font-size: 9pt;
  }
}

.left-span-inactive {
  color: hsl(1, 0%, 80%);
  @media screen and (max-width: 1000px) {
    display: none;
  }
}

.left-span-div {
  min-height: 20pt;
  @media screen and (max-width: 1000px) {
    min-height: 0;
  }
}

.right-span {
  padding-left: 7px;
  @media screen and (max-width: 1000px) {
    padding-left: 3px;
  }
}

.right-span-active {
  color: hsl(208, 45%, 70%);
}

.right-span-inactive {
  color: hsl(1, 0%, 80%);
}

</style>
