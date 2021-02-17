<template>
  <canvas id="chart"></canvas>
</template>
<script>
import Chart from "chart.js";

export default {
  props: {
    data: {
      default: {
        labels: ["Red", "Blue", "Yellow", "Green", "Purple", "Orange"],
        datasets: [
          {
            label: "# of Votes",
            data: [12, 19, 3, 5, 2, 3],
          },
        ],
      },
    },
    options: {
      default: {
        scales: {
          yAxes: [
            {
              ticks: {
                beginAtZero: true,
              },
            },
          ],
        },
      },
    },
  },
  data: () => ({
    chart: null,
  }),
  watch: {
    data: function () {
      this.chart.chart.data = this.data;
      this.chart.update();
    },
    options: function () {
      this.chart.update();
    },
  },
  methods: {
    createChart() {
      let chartEl = document.getElementById("chart");
      this.chart = new Chart(chartEl, {
        type: "line",
        data: this.data,
        options: this.options,
      });
    },
  },
  mounted() {
    this.createChart();
  },
};
</script>