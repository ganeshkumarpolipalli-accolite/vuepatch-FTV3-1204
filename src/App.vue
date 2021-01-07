<template>
  <div id="app">
    <fusioncharts
      :type="type"
      :width="width"
      :height="height"
      :dataFormat="dataFormat"
      data-empty-message="Data is loading"
      :dataSource="dataSource"
      ref="fc"
      @renderComplete="renderComplete"
    ></fusioncharts>
  </div>
</template>

<script>
import Vue from "vue";
import VueFusionCharts from "vue-fusioncharts";
import FusionCharts from "fusioncharts";
import TimeSeries from "fusioncharts/fusioncharts.timeseries";

Vue.use(VueFusionCharts, FusionCharts, TimeSeries);

const getRandomInt = (min, max) => {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min + 1)) + min;
};

let feedDataTime = 1595633583000;

export default {
  name: "App",

  data: function() {
    return {
      sampleData: [],
      type: "timeseries",
      width: "100%",
      height: "450",
      dataFormat: "json",
      strict: true,
      dataSource: {
        caption: {
          text: "NewYork City - Temparature variations (2019)",
        },
        data: null,
        subcaption: {
          text: "Daily min, max and average temperatures",
        },
        chart: {
          showlegend: 0,
          showtooltip: 1,
        },
        yaxis: [
          {
            title: "Temperature in Celcius",
            plot: [
              {
                value: {
                  high: "High",
                  low: "Low",
                },
                type: "area-range",
                name: "Area ranged plot",
              },
            ],
          },
        ],
      },
      schema: [
        {
          name: "Date",
          type: "date",
          format: "%Q",
        },
        {
          name: "Low",
          type: "number",
        },
        {
          name: "High",
          type: "number",
        },
        {
          name: "Value",
          type: "number",
        },
      ],
    };
  },
  mounted: function() {
    for (let i = 0; i < 300; i++) {
      const date = 1595633284000 + i * 1000;
      const low = getRandomInt(1, 20);
      const value = getRandomInt(30, 50);
      const high = getRandomInt(70, 100);
      this.sampleData.push([date, low, high, value]);
    }
    const fusionTable = new FusionCharts.DataStore().createDataTable(
      this.sampleData,
      this.schema
    );
    this.dataSource.data = fusionTable;
  },
  methods: {
    renderComplete: function() {
      setInterval(() => {
        if (this.$refs && this.$refs.fc && this.$refs.fc.chartObj) {
          const chartObj =
            this.$refs != undefined ? this.$refs.fc.chartObj : undefined;

          try {
            if (chartObj) {
              chartObj.feedData([
                [
                  (feedDataTime += 1000),
                  getRandomInt(1, 20),
                  getRandomInt(70, 100),
                  getRandomInt(30, 50),
                ],
              ]);
            }
          } catch (err) {
            console.log(err);
          }
        }
      }, 500);
    },
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
