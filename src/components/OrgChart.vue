<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    :width="orgChartWidth"
    :height="orgChartHeight"
    x="0"
    y="0">
    <g ref="orgChart">
      <OrgChartNodeWrapper
        :org-data="orgData"
        @refreshParent="refreshOrgChart"></OrgChartNodeWrapper>
    </g>
  </svg>
</template>

<script>
import OrgChartNodeWrapper from "./OrgChartNodeWrapper.vue";

export default {
  name: "OrgChart",
  components: {
    OrgChartNodeWrapper
  },

  props: {
    orgData: {
      type: Object,
      required: true
    }
  },

  data() {
    return {
      orgChartWidth: 0,
      orgChartHeight: 0
    };
  },

  methods: {
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
    this.refreshOrgChart()
  }
};
</script>
