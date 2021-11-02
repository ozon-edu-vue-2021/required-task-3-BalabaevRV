<template>
  <div class="menu" v-click-outside="onClickOutside">
    <div class="toolbar">
      <div class="toolbar__header">
        <template v-if="!isUserOpenned">
          <h3>Информация</h3>
        </template>
        <template v-else>
          <div class="action">
            <div class="arrow" @click="closeProfile"></div>
          </div>
          <h3>Профиль</h3>
        </template>
      </div>
      <div class="toolbar__actions"></div>
    </div>
    <div class="content">
      <div v-if="!isUserOpenned" class="legend">
        <div class="legend__data">
          <div v-if="legend.length > 0" class="legend__items">
            <draggable>
              <LegendItem
                v-for="(item, index) in legend"
                :key="index"
                :color="item.color"
                :text="item.text"
                :counter="item.counter"
                class="legend__item"
              />
            </draggable>
          </div>
          <span v-else class="legend--empty"> Список пуст </span>
        </div>
        <div class="legend__chart">
          <Doughnut ref="chart" />
        </div>
      </div>
      <div v-else class="profile">
        <div v-if="!person" class="profile__empty">Место пустое</div>
        <PersonCard v-if="person" :person="person" />
      </div>
    </div>
  </div>
</template>

<script>
import LegendItem from "./SideMenu/LegendItem.vue";
import PersonCard from "./SideMenu/PersonCard.vue";
import legend from "@/assets/data/legend.json";
import { Doughnut } from "vue-chartjs";
import Draggable from "vuedraggable";
import tables from "@/assets/data/tables.json";
import vClickOutside from "v-click-outside";

export default {
  props: {
    isUserOpenned: {
      type: Boolean,
      default: false,
    },
    person: {
      type: Object,
      default: null,
    },
  },
  directives: {
    clickOutside: vClickOutside.directive,
  },
  components: {
    LegendItem,
    PersonCard,
    Doughnut,
    Draggable,
  },
  data() {
    return {
      legend: [],
    };
  },
  created() {
    this.loadLegend();
  },
  methods: {
    loadLegend() {
      this.legend = legend;
    },
    closeProfile() {
      this.$emit("update:isUserOpenned", false);
    },
    makeChart() {
      if (!this.isUserOpenned) {
        const chartData = {
          labels: this.legend.map((legendItem) => legendItem.text),
          datasets: [
            {
              label: "Легенда",
              backgroundColor: this.legend.map(
                (legendItem) => legendItem.color
              ),
              data: this.legend.map((legendItem) => legendItem.counter),
            },
          ],
        };
        const options = {
          legend: {
            display: false,
          },
        };
        this.$refs.chart.renderChart(chartData, options);
      }
    },
    setCounterInLegend() {
      this.legend.forEach(
        (legendItem) =>
          (legendItem.counter = tables.filter(
            (table) => table.group_id === legendItem.group_id
          ).length)
      );
    },
    onClickOutside() {
      if (this.isUserOpenned) {
        this.closeProfile();
      }
    },
  },
  mounted() {
    this.setCounterInLegend();
  },
  updated() {
    this.makeChart();
  },
};
</script>

<style scoped>
.menu {
  border-left: 1px solid #ccd8e4;
  padding: 24px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.toolbar .toolbar__actions button {
  font-size: 0.76rem;
  text-transform: uppercase;
  letter-spacing: 0.08rem;
  padding: 2px 6px;
}

.toolbar__header {
  display: flex;
  align-items: center;
  margin-bottom: 12px;
}

.toolbar__header .action {
  cursor: pointer;
  margin-right: 14px;
  width: 20px;
  height: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.toolbar__header .action .arrow {
  width: 15px;
  height: 15px;
  border-top: 4px solid blue;
  border-right: 4px solid blue;
  transform: rotate(-135deg);
}

h3 {
  margin: 0;
  font-size: 24px;
}

.content {
  flex: 1;
}

.content .legend {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  height: 100%;
}

.content .legend .legend__data {
  display: flex;
  margin-bottom: 20px;
}

.content .legend .legend__items {
  flex: 1;
  width: 100%;
}

.content .legend .legend__items .legend__item:not(:first-child) {
  margin-top: 16px;
}

.content .legend .legend__items .legend__item {
  cursor: pointer;
}

.content .legend .legend__items .legend__item.sortable-chosen {
  opacity: 25%;
}

.content .legend .legend--empty {
  align-self: center;
  width: 100%;
  text-align: center;
}

.profile {
  padding-top: 20px;
}
</style>
