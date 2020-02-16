<template>
  <div id="app">
      <!-- :width="orgChartWidth || ((maxChildrenOnOneLevel * 100) + 100)"
      :height="orgChartHeight || (300)" -->
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="5000"
      height="1000"
      x="0"
      y="0"
    >
      <g ref="orgChart">
        <OrgChart v-if="orgData" :org-data="orgData" :level-items-counts="orgChartLevels"></OrgChart>
      </g>
    </svg>
    <div>{{ orgChartLevels }}</div>
    <div>{{ maxChildrenOnOneLevel }}</div>
  </div>
</template>

<script>
import OrgChart from "./components/OrgChart.vue";

export default {
  name: "App",
  components: {
    OrgChart
  },

  data() {
    return {
      orgData: null,
      orgChartLevels: {},
      orgChartWidth: 0,
      orgChartHeight: 0
    };
  },

  computed: {
    maxChildrenOnOneLevel() {
      return this.orgChartLevels[Math.max(...Object.keys(this.orgChartLevels))];
    }
  },

  methods: {
    generateOrgdata(level = 1, previousName = "") {
      const org = {
        name: `A-${level} (${previousName || "-"})`,
        children: [],
        width: 100
      };
      const childrenSize = level === 5 ? 0 : Math.floor(Math.random() * 11);
      for (var i = 0; i < childrenSize; i++) {
        org.children.push(this.generateOrgdata(level + 1, `${level}-${i}`));
      }
      return org;
    }
  },

  mounted() {
    this.orgData = this.generateOrgdata();
    setTimeout(() => {
      this.orgChartWidth = this.$refs.orgChart.getBoundingClientRect().width;
      this.orgChartHeight = this.$refs.orgChart.getBoundingClientRect().height;
      console.log(this.orgData);
      this.orgData.width = this.orgChartWidth;
    }, 500);
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
