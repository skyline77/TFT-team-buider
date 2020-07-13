<template>
  <button type="button"><slot></slot></button>
</template>

<script>

export default {
  name: 'PageButtonButton',
};
</script>

<style lang="less">

.button {
  .button-font();
  .button-layout();
  .button-style();
  .button-animation();
}

.button-font(){
  font-size: 10pt;
  white-space:nowrap;
  @media screen and (max-width: 1000px) {
    font-size: 9pt;
  }
}

.button-layout(){
  margin: 4px 6px;
  padding: 0 8px;
  height: 30px;
  min-width: 100px;
  border: solid 1px;
  border-radius: 5px;
  &:hover {
    cursor: pointer;
  }
  @media screen and (max-width: 1000px) {
    margin: 2px 4px;
    padding: 2 8px;
    height: 30px;
    min-width: 70px;
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

.active-team {
    color: hsl(189, 62%, 46%);
    border-color: hsl(189, 62%, 46%);
}

.active-cost { // 也应该定义 active nonactive
    color: hsl(208, 65%, 45%);
    border-color: hsl(208, 65%, 45%);
}

.inactive-delete,
.inactive-add {
  color:rgb(202, 202, 202);
  border-color:rgb(202, 202, 202);
  &:hover {
    cursor: not-allowed;
  }
}

</style>
