<template>
  <div>
    <div
      v-for="(p, index) in placement"
      :style="cellStyle(p)"
      :key="`placement-${index}`"
      class="placement-cell"
    >
      <div class="placement-text" :style="textStyle(p)">{{ p.text }}</div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Layout from "@/layout/harvest-layout.json";
import { calculateCellPosition } from "@/utils/cell-calc";
import { CellPlacement } from "@/types/CellPlacement";
import { isSeed } from "../utils/placement-util";

export default Vue.extend({
  name: "PlacementDisplay",
  props: {
    isConnecting: {
      default: false
    },
    connectingSeedAlpha: {
      default: 0.3
    },
    defaultItemAlpha: {
      default: 0.7
    },
    size: { type: [Number], default: 20 },
    placement: {},
    linkPoint: {
      default: (): CellPlacement[] => []
    }
  },
  data() {
    return {
      cellData: Layout,
      backgroundColor: ["156, 39, 176", "245, 127, 23", "33, 150, 243"]
    };
  },
  methods: {
    textStyle(placement: CellPlacement) {
      return {
        "z-index": this.linkPoint.includes(placement) ? 9 : null
      };
    },
    cellStyle(placement: CellPlacement) {
      const [xPos, yPos] = calculateCellPosition(
        [+placement.x, +placement.y],
        this.size
      );
      const style = {
        height: `${this.size}px`,
        width: `${this.size}px`,
        top: `${xPos}px`,
        left: `${yPos}px`,
        "background-color": `rgba(${
          this.backgroundColor[placement.color ?? -1]
        }, ${this.defaultItemAlpha})`,
        opacity: 1
      };

      if (this.isConnecting && isSeed(placement)) {
        style.opacity = this.connectingSeedAlpha;
      }
      return style;
    }
  }
});
</script>

<style scoped>
.placement-text {
  position: relative;
  text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
    1px 1px 0 #000;
}
.placement-cell {
  pointer-events: none;
  border: 1px solid rgba(0, 0, 0, 0.5);
  margin: 0;
  padding: 0;
  position: absolute;
  user-select: none;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
