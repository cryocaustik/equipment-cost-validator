<template>
  <v-container>
    <v-card>
      <v-card-text>
        <div class="text-uppercase" id="caption">
          <strong>Q:</strong> Does expensive equipment pay for itself?
          <br />
          <strong>A:</strong>
          <span class="red--text"> {{ format(equipment_cost, "$0.0a") }}</span>
          worth of equipment that saves a user
          <span class="teal--text">{{ hours_saved_per_day }}hr(s)</span> per
          day, will pay for itself within
          <span class="green--text">
            {{ format(days_to_intersect) }} work days </span
          >; creating an annual savings of
          <span class="blue--text">{{
            format(salary_saved_per_year, "$0.0a")
          }}</span>
        </div>
      </v-card-text>

      <v-card-text>
        <Chart :data="chartData" :options="chartOptions" />
      </v-card-text>

      <v-card-subtitle> Parameters </v-card-subtitle>

      <v-card-text>
        <v-row>
          <v-col>
            <v-text-field
              v-model="salary"
              type="number"
              label="Salary"
            ></v-text-field>
          </v-col>

          <v-col>
            <v-text-field
              v-model="annual_work_hours"
              type="number"
              label="Annual Work Hours"
            ></v-text-field>
          </v-col>

          <v-col>
            <v-text-field
              v-model="hourly_rate"
              type="number"
              disabled
              label="Hourly Rate (salary/hours)"
            ></v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col>
            <v-text-field
              v-model="equipment_cost"
              type="number"
              label="Equipment Cost"
            ></v-text-field>
          </v-col>

          <v-col>
            <v-text-field
              v-model="hours_saved_per_day"
              type="number"
              label="Hours Saved per Day"
              min="0.1"
              step="0.1"
            ></v-text-field>
          </v-col>

          <v-col>
            <v-text-field
              v-model="salary_saved_per_year"
              type="text"
              disabled
              label="Annual Salary Savings"
            ></v-text-field>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>
  </v-container>
</template>
<script>
import Chart from "./Chart";
import numeral from "numeral";

export default {
  name: "EquipmentCost",
  data: () => ({
    salary: 70000,
    annual_work_hours: 2050,
    equipment_cost: 1500,
    hours_saved_per_day: 0.5,
    chartOptions: {
      tooltips: {
        intersect: false,
        mode: "index",
        responsive: true,
      },
      scales: {
        yAxes: [
          {
            display: true,
            scaleLabel: {
              display: true,
              labelString: "Cost",
            },
          },
        ],
        xAxes: [
          {
            display: true,
            scaleLabel: {
              display: true,
              labelString: "Days",
            },
          },
        ],
      },
    },
  }),
  computed: {
    hourly_rate() {
      return (this.salary / this.annual_work_hours).toFixed(2);
    },
    daily_rate() {
      return (this.salary / this.annual_work_days).toFixed(2);
    },
    annual_work_days() {
      return this.annual_work_hours / 8;
    },
    salary_saved_per_day() {
      return parseFloat(this.hours_saved_per_day) * this.hourly_rate;
    },
    salary_saved_per_year() {
      return this.format(
        (this.annual_work_hours / 8) * this.salary_saved_per_day,
        "$0.0a"
      );
    },
    days_to_intersect() {
      return Math.ceil(this.equipment_cost / this.salary_saved_per_day);
    },
    days_range() {
      let days = [...Array(this.days_to_intersect + 1).keys()];
      // days.shift(1);
      return days;
    },
    equipment_cost_overtime() {
      return this.days_range.map(() => this.equipment_cost);
    },
    salary_saved_overtime() {
      return this.days_range.map((d) =>
        (d * this.salary_saved_per_day).toFixed(2)
      );
    },
    chartData() {
      return {
        labels: this.days_range,
        datasets: [
          {
            label: "Salary Cost Saved",
            data: this.salary_saved_overtime,
            backgroundColor: "rgba(85, 102, 198, 0.2)",
          },
          {
            label: "Equipment Cost",
            data: this.equipment_cost_overtime,
            backgroundColor: "rgba(184, 82, 82, 0.2)",
          },
        ],
      };
    },
  },
  methods: {
    format(n, format = "") {
      return numeral(n).format(format);
    },
  },
  components: {
    Chart,
  },
  mounted() {
    console.log(this.chartData);
  },
};
</script>

<style scoped>
#caption {
  font-size: 2em;
  line-height: 1.25em;
  font-weight: 300 !important;
}
</style>