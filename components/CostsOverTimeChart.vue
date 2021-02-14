<template>
  <div ref="container" class="chart-container"></div>
</template>

<script>
/* global echarts */

export default {
  props: {
    data: {
      required: true,
      type: Array,
    },

    scenario: {
      required: true,
      type: Object,
    },
  },

  data() {
    const currentYear = new Date().getFullYear()

    const series = [
      {
        data: this.data.map((c) => c.amount),
        name: `${this.scenario.name} repayment`,
        stack: this.scenario.key,
        type: 'line',
        areaStyle: {},
      },
      {
        data: this.data.map((c) => c.interest),
        name: `${this.scenario.name} interest`,
        stack: this.scenario.key,
        type: 'line',
        areaStyle: {},
      },
    ]

    return {
      chart: null,

      chartOptions: {
        series,
        tooltip: {
          trigger: 'axis',
        },
        xAxis: {
          boundaryGap: false,
          data: this.data.map((c) => currentYear + c.year),
          type: 'category',
        },
        yAxis: {},
      },
    }
  },

  mounted() {
    if (!this.$isServer) {
      this.createChart(this.$refs.container)
    }
  },

  methods: {
    createChart(container) {
      this.chart = echarts.init(container)

      this.chart.setOption(this.chartOptions)
    },
  },
}
</script>

<style scoped>
.chart-container {
  height: 25vh;
  width: 100%;
}
</style>
