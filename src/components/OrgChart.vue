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
      :is-last-level="orgData.children.length===0 || !isShowChildren"
      :is-show-next-level.sync="isShowChildren"
      @refreshView="refreshWidth"
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
        @refreshFirstLevel="$emit('refreshFirstLevel')"
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
      isShowChildren: false
    };
  },

  computed: {
    yVal() {
      return (this.level - 1) * 80;
    },

    xVal() {
      return this.startX;
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
    },

    refreshWidth () {
      setTimeout(() => {
        if (!this.$refs.childrenGroup) {
          this.$emit("update:width", 100);
          return;
        }
        const pos = this.$refs.childrenGroup.getBoundingClientRect();
        this.$emit("update:width", pos.width);
        this.$emit('refreshFirstLevel')
      }, 100);
    }
  },

  mounted() {
  }
};
</script>

<style scoped>
</style>
