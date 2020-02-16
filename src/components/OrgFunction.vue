<template>
  <!-- :width="width" :height="height" -->
  <svg :width="width" :x="x" :y="y" xmlns="http://www.w3.org/2000/svg">
    <rect :x="xpos" y="10" width="100" height="50" fill="#ccc"></rect>
    <!-- <text :x="xpos" y="30">{{ width }}, {{ height }}, {{ x }}, {{ y }}</text> -->
    <text :x="xpos" y="30">{{ name }}</text>
    <line v-if="!isLastLevel" :x1="xpos + 50" y1="60" :x2="xpos + 50" y2="80" stroke="black"></line>
    <line v-if="!isFirstLevel" :x1="xpos + 50" y1="0" :x2="xpos + 50" y2="10" stroke="black"></line>
  </svg>
</template>

<script>
export default {
  name: "OrgFunction",

  props: ["name", "width", "height", "x", "y", "isFirstLevel", "isLastLevel"],

  computed: {
    xpos() {
      return this.width / 2 - 50;
    }
  }
};
</script>

<style scoped>
</style>
