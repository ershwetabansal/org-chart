<template>
  <div id="app">
    <OrgChart
      v-if="orgData"
      :org-data="orgData"
      @refreshParent="refreshOrgChart"></OrgChart>
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
      orgData: null
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
    }
  },

  mounted() {
    this.orgData = this.generateOrgdata();
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
