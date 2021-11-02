<template>
  <div class="map">
    <h3>Карта офиса</h3>
    <div v-if="!isLoading" class="map-root">
      <MapSVG ref="mapRoot" />
      <tableSVG ref="tableSVG" v-show="false" />
    </div>
    <div v-else>Loading...</div>
  </div>
</template>

<script>
import MapSVG from "@/assets/images/map.svg?inline";
import tableSVG from "@/assets/images/workPlace.svg?inline";
import * as d3 from "d3";
import tables from "@/assets/data/tables.json";
import legend from "@/assets/data/legend.json";

export default {
  components: {
    MapSVG,
    tableSVG,
  },
  props: ["isUserOpenned"],
  data() {
    return {
      isLoading: false,
      svg: null,
      g: null,
      tables: [],
      tableSVG: null,
      selectTableColor: "",
      selectTable: null,
    };
  },
  mounted() {
    this.svg = d3.select(this.$refs.mapRoot);
    this.g = this.svg.select("g");
    this.tableSVG = d3.select(this.$refs.tableSVG);
    this.tables = tables;
    if (this.g) {
      this.drawTables();
    } else {
      console.log("Нет поля для отрисовки");
    }
  },
  watch: {
    isUserOpenned() {
      if (!this.isUserOpenned) {
        this.selectTable.setAttribute("fill", this.selectTableColor);
        this.selectTable = null;
      }
    },
  },
  methods: {
    drawTables() {
      const svgTablesGroup = this.g.append("g").classed("groupPlaces", true);
      this.tables.map((table) => {
        const svgTable = svgTablesGroup
          .append("g")
          .attr("transform", `translate(${table.x}, ${table.y}), scale(0.5)`)
          .attr("id", table._id)
          .classed("employer-place", true)
          .on("click", this.clickTable);

        svgTable
          .append("g")
          .attr("transform", `rotate(${table.rotate || 0})`)
          .html(this.tableSVG.html())
          .attr(
            "fill",
            legend.find((it) => it.group_id === table.group_id)?.color ??
              "transparent"
          );
      });
    },
    clickTable(e) {
      e.stopPropagation();
      if (this.selectTable) {
        this.selectTable.setAttribute("fill", this.selectTableColor);
      }
      const newTable = e.currentTarget.querySelector("g");
      this.selectTable = newTable;
      this.selectTableColor = newTable.getAttribute("fill");
      newTable.setAttribute("fill", "lightgreen");
      this.$emit("clickTable", e.currentTarget.id);
    },
  },
};
</script>

<style scoped>
.map {
  height: 100%;
  width: 100%;
  padding: 24px;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.map-root {
  height: 100%;
  width: 100%;
  overflow: hidden;
  box-sizing: border-box;
}

h3 {
  margin-top: 0px;
}

::v-deep svg {
  height: 100%;
  width: 100%;
}

::v-deep .table {
  cursor: pointer;
}
</style>
