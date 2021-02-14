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

    return {
      chart: null,

      chartOptions: {
        series: this.createSeries(),
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

  watch: {
    data() {
      this.chart.setOption({
        series: this.createSeries(),
      })
    },
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

    createSeries() {
      return [
        {
          areaStyle: {},
          data: this.data.map((c) => c.amount),
          name: 'Repayment',
          showSymbol: false,
          stack: 'total',
          type: 'line',
        },
        {
          areaStyle: {},
          data: this.data.map((c) => c.interest),
          name: 'Interest',
          showSymbol: false,
          stack: 'total',
          type: 'line',
        },
      ]
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
