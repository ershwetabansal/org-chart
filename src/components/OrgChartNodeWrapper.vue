<template>
  <g>
    <!-- Level 1 -->
    <OrgChartNode
      :name="orgData.name"
      :width="orgData.width || 100"
      :height="100"
      :x="startX"
      :y="depth"
      :is-first-level="level === 1"
      :is-last-level="orgData.children.length===0 || !isShowChildren"
      :is-show-next-level.sync="isShowChildren"
      @refreshView="refreshWidth"
    ></OrgChartNode>

    <!-- <rect
      v-if="orgData.children.length && isShowChildren"
      :x="startX + 50"
      :y="depth + 80"
      :width="childrenWidth"
      height="50"></rect> -->
    <line v-if="orgData.children.length && isShowChildren" :x1="startX + 50" :y1="depth + 80" :x2="(startX + orgData.children.reduce((acc, child) => (acc + child.width), 0))" :y2="depth + 80" stroke="black"></line>
    <g ref="childrenGroup" v-if="orgData.children.length && isShowChildren">
      <OrgChartNodeWrapper
        v-for="(data, index) in orgData.children"
        :key="index"
        :org-data="data"
        :width.sync="data.width"
        :start-x="getChildrenStartX(index)"
        :level="level + 1"
        :x-level="index + xLevel"
        @refreshParent="refreshMeAndParent"
      ></OrgChartNodeWrapper>
    </g>
  </g>
</template>

<script>
// child component has to keep telling the parent
// component about the size it has fitted in
import OrgChartNode from "./OrgChartNode.vue";
import OrgChartNodeWrapper from "./OrgChartNodeWrapper.vue";

export default {
  name: "OrgChartNodeWrapper",

  components: {
    OrgChartNode,
    OrgChartNodeWrapper
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
    depth() {
      return (this.level - 1) * 80;
    },

    childrenWidth () {
      return this.orgData.children.reduce((acc, child) => (acc + child.width), 0)
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
          this.$emit('refreshParent')
          return;
        }
        const pos = this.$refs.childrenGroup.getBoundingClientRect();
        this.$emit("update:width", pos.width);
        this.$emit('refreshParent')
      }, 100);
    },

    refreshMeAndParent () {
      this.refreshWidth()
    }
  },

  mounted() {
  }
};
</script>

<style scoped>
</style>
