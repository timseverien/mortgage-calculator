<template>
  <div ref="container" class="chart-container"></div>
</template>

<script>
import * as echarts from 'echarts'

const colors = [
  '#007bff',
  '#17a2b8',
  '#20c997',
  '#28a745',
  '#343a40',
  '#6610f2',
  '#6c757d',
  '#6f42c1',
  '#dc3545',
  '#e83e8c',
  '#fd7e14',
  '#ffc107',
]

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
          snap: true,
          lineStyle: {
            color: '#000',
            opacity: 1,
          },
        },
        xAxis: {
          boundaryGap: false,
          data: this.data.map((c) => currentYear + c.year),
          type: 'category',
        },
        yAxis: {},
        color: colors,
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
          areaStyle: {
            opacity: 0.8,
          },
          data: this.data.map((c) => c.amount),
          lineStyle: { width: 0 },
          name: 'Repayment',
          showSymbol: false,
          stack: 'total',
          type: 'line',
        },
        {
          areaStyle: {
            opacity: 0.8,
          },
          data: this.data.map((c) => c.interest),
          lineStyle: { width: 0 },
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
