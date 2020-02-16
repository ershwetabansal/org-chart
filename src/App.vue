<template>
  <div id="app">
    <svg
      xmlns="http://www.w3.org/2000/svg"
      :width="orgChartWidth"
      :height="orgChartHeight"
      x="0"
      y="0"
    >
      <g ref="orgChart">
        <OrgChart v-if="orgData" :org-data="orgData" @refreshParent="refreshOrgChart"></OrgChart>
      </g>
    </svg>
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
      orgChartWidth: 0,
      orgChartHeight: 0
    };
  },

  methods: {
    generateOrgdata(level = 1) {
      const org = {
        name: `A-${level}`,
        children: [],
        width: 100
      };
      const childrenSize = level === 5 ? 0 : Math.floor(Math.random() * 4);
      for (var i = 0; i < childrenSize; i++) {
        org.children.push(this.generateOrgdata(level + 1));
      }
      org.name += ` (${org.children.length})`
      return org;
    },

    refreshOrgChart () {
      setTimeout(() => {
        const chart = this.$refs.orgChart.getBoundingClientRect()
        this.orgChartWidth = chart.width;
        this.orgChartHeight = chart.height;
        this.orgData.width = this.orgChartWidth;
      }, 100);
    }
  },

  mounted() {
    this.orgData = this.generateOrgdata();
    this.refreshOrgChart()
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
