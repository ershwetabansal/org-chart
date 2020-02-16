<template>
  <g>
    <!-- Level 1 -->
    <org-function
      :name="orgData.name"
      :width="orgData.width || 100"
      :height="100"
      :x="startX"
      :y="yVal"
      :is-first-level="level === 1"
      :is-last-level="orgData.children.length===0"
      :is-show-next-level.sync="isShowChildren"
    ></org-function>

    <line v-if="orgData.children.length && isShowChildren" :x1="startX + 50" :y1="yVal + 80" :x2="(startX + orgData.children.reduce((acc, child) => (acc + child.width), 0))" :y2="yVal + 80" stroke="black"></line>
    <g ref="childrenGroup" v-if="orgData.children.length && isShowChildren">
      <org-chart
        v-for="(data, index) in orgData.children"
        :key="index"
        :org-data="data"
        :width.sync="data.width"
        :start-x="getChildrenStartX(index)"
        :level="level + 1"
        :x-level="index + xLevel"
        :level-items-counts="levelItemsCounts"
      ></org-chart>
    </g>
  </g>
</template>

<script>
// child component has to keep telling the parent
// component about the size it has fitted in
import OrgFunction from "./OrgFunction.vue";
import OrgChart from "./OrgChart.vue";

export default {
  name: "OrgChart",

  components: {
    OrgFunction,
    OrgChart
  },

  props: {
    orgData: {
      type: Object
    },

    level: {
      type: Number,
      required: false,
      default: 1
    },

    xLevel: {
      type: Number,
      required: false,
      default: 1
    },

    levelItemsCounts: {
      type: Object,
      required: false,
      default() {
        return {};
      }
    },

    width: {
      type: Number,
      required: false
    },

    startX: {
      type: Number,
      required: false,
      default: 0
    }
  },

  data() {
    return {
      childrenWidth: 0,
      childrenLeft: 0,
      isShowChildren: false
    };
  },

  computed: {
    yVal() {
      return (this.level - 1) * 80;
    },

    xVal() {
      // if (this.childrenLeft) {
      //   return this.childrenLeft
      // }
      return this.startX;
      // return this.startX + (this.xLevel - 1) * 110;
    },

    fnWidth() {
      const width =
        this.childrenWidth || (this.orgData.children.length || 1) * 60;
      return width > 100 ? width : 100;
    }
  },

  methods: {
    getChildrenStartX(index) {
      if (index === 0) {
        return this.startX;
      }
      return (
        this.startX +
        this.orgData.children
          .slice(0, index)
          .reduce((acc, child) => child.width + acc + 10, 0)
      );
    }
  },

  mounted() {
    this.$set(
      this.levelItemsCounts,
      this.level,
      this.levelItemsCounts[this.level] || 0
    );
    this.$set(
      this.levelItemsCounts,
      this.level,
      Math.max(this.levelItemsCounts[this.level], this.xLevel)
    );
    setTimeout(() => {
      if (!this.$refs.childrenGroup) {
        return;
      }
      const pos = this.$refs.childrenGroup.getBoundingClientRect();
      this.childrenWidth = pos.width;
      this.childrenLeft = pos.left;
      this.$emit("update:width", this.childrenWidth);
    }, 100);
  }
};
</script>

<style scoped>
</style>
