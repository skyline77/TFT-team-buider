<template>
  <table>
    <thead>
      <tr>
        <th id="lefttop"></th>
        <MyTh
          v-for="origin in originsList"
          :key="origin.name"
          :class="[origin.classes, 'th-' + origin.number.length]"
          :text="origin.name"
          :numbers="origin.number"
          :whichSpanActive="findFirstIndex(origin.number, countOrigins(origin.name))"
          :currentNumber="countOrigins(origin.name)"
          :isActive="origin.classes.active"
        />
      </tr>
    </thead>
    <tbody>
      <tr v-for="clas in classesList" :key="clas.name">
        <MyTh
          :class="[clas.classes, 'th-' + clas.number.length]"
          :text="clas.name"
          :numbers="clas.number"
          :whichSpanActive="findFirstIndex(clas.number, countClasses(clas.name))"
          :currentNumber="countClasses(clas.name)"
          :isActive="clas.classes.active"
        />

        <td
          v-for="origin in originsList"
          :key="origin.name"
        >
          <div class="flex-td">
            <MyImage
              v-for="champion in championsFilter(origin.name, clas.name)"
              :key="champion.name"
              :count="countOriginClass(origin.name, clas.name)"
              @click.native="clickImg(champion)"
              :imgfolder="imgfolder"
              :champion="champion"
            />
          </div>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
// 总结：动态引用资源，需要require
// src = "@/assets/Aatrox.png" 可以
// :src = "'logo' | imageUrl" 不行
// :src = " '@/assets/Aatrox.png' " 也不行
// :src = " require('@/assets/Aatrox.png') " 可以
// 此外，require不能使用方法、filter
// https://stackoverflow.com/questions/42908116/webpack-critical-dependency-the-request-of-a-dependency-is-an-expression

import MyTh from './MyTh.vue';
import MyImage from './MyImage.vue';

export default {
  name: 'MyTable',
  components: { MyTh, MyImage },
  props: {
    champions: Array,
    classes: Array,
    origins: Array,
    imgfolder: String,
  },
  computed: {
    originsList() {
      return this.origins.map((origin) => ({
        name: origin.name,
        number: origin.number,
        classes: {
          active: this.countOrigins(origin.name) >= origin.number[0],
        },
      }));
    },
    classesList() {
      return this.classes.map((clas) => ({
        name: clas.name,
        number: clas.number,
        classes: {
          active: this.countClasses(clas.name) >= clas.number[0],
        },
      }));
    },
  },

  methods: {
    // count
    championsFilter(originName, className) {
      return this.champions.filter(
        (champion) => champion.origin.includes(originName)
          && champion.clas.includes(className),
      );
    },
    countOrigins(origin) {
      // 忘了champion.origin 是一个数组
      const luxExtraOrigin = this.champions.some(
        (champion) => champion.name.split('_')[0] === 'Lux'
          && champion.classes.show === true
          && champion.origin.includes(origin),
      )
        ? 1
        : 0;

      return (
        this.champions.filter(
          (champion) => champion.origin.includes(origin) && champion.classes.show === true,
        ).length + luxExtraOrigin
      );
    },
    countClasses(clas) {
      const qiyanaExtraClass = clas === '刺客'
        ? Math.max(
          this.champions.filter(
            (champion) => champion.name.split('_')[0] === 'Qiyana'
                  && champion.classes.show === true,
          ).length - 1,
          0,
        )
        : 0;
      const luxExtraClass = clas === '大元素使'
        ? Math.max(
          this.champions.filter(
            (champion) => champion.name.split('_')[0] === 'Lux'
                  && champion.classes.show === true,
          ).length - 1,
          0,
        )
        : 0;

      return (
        this.champions.filter(
          (champion) => champion.clas.includes(clas) && champion.classes.show === true,
        ).length
        - qiyanaExtraClass
        - luxExtraClass
      );
    },
    countOriginClass(origin, clas) {
      return this.champions.filter(
        (champion) => champion.clas.includes(clas) && champion.origin.includes(origin),
      ).length;
    },

    findFirstIndex(list, num) {
      return list.filter((item) => item <= num).length - 1;
    },

    clickImg(champion) {
      if (champion.name.indexOf('_') === -1) {
        // 普通情况
        champion.classes.show = !champion.classes.show;
      } else {
        // 是Qiyana或Lux
        const firstName = (name) => name.split('_')[0];
        const lastName = (name) => name.split('_')[1];

        const onlyThis = !this.champions.some(
          (item) => firstName(item.name) === firstName(champion.name)
              && lastName(item.name) !== lastName(champion.name)
              && item.classes.show === true,
        ) && champion.classes.show === true; // 不存在 show && firstName同 && lastName不同 && 这个是true

        if (onlyThis) {
          // 只有这个是亮的
          champion.classes.show = false;
        } else {
          //
          this.champions.forEach((champ) => {
            if (
              firstName(champ.name) === firstName(champion.name)
              && lastName(champ.name) === lastName(champion.name)
            ) {
              // 同英雄，同class
              champ.classes.show = true;
            } else if (
              firstName(champ.name) === firstName(champion.name)
              && lastName(champ.name) !== lastName(champion.name)
            ) {
              // 同英雄，不同class
              champ.classes.show = false;
            } else {
              // 别的英雄，忽略
            }
          });
          champion.classes.show = true;
        }
      }
    }, // filter不能有副作用 [1,-2,3,-4].filter(x => x > 0).forEach(x => x = -x)
  },
};
</script>

<style lang="less" scoped>

table {
  /* table-layout: fixed; */
  margin: 10px auto;
  background-color: hsl(1, 0%, 98%);
  border-collapse: collapse;
  @media screen and (max-width: 1000px) {
    margin-bottom: 20px;
    margin: 10px 5px;
  }
}

.flex-td {
  display: flex;
  justify-content: center;
}

td {
  padding: 7px 5px;
  @media screen and (max-width: 1000px) {
      padding: 3px 1px 1px;
  }
}

th, td {
  border: solid 1px rgb(230, 230, 230);
}
</style>
