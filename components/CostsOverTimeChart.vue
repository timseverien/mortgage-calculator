<template>
  <div ref="container" class="chart-container"></div>
</template>

<script>
import * as echarts from 'echarts'

const colors = [
  '#007bff',
  '#6610f2',
  '#6f42c1',
  '#e83e8c',
  '#dc3545',
  '#fd7e14',
  '#ffc107',
  '#28a745',
  '#20c997',
  '#17a2b8',
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
    const { legend, series } = this.createSeries()
    const costsFormatter = new Intl.NumberFormat(undefined, {
      currency: 'EUR',
      maximumFractionDigits: 2,
      minimumFractionDigits: 2,
      style: 'currency',
    })

    return {
      chart: null,
      chartOptions: {
        color: colors,
        legend: { data: legend },
        series,

        tooltip: {
          axisPointer: {
            animation: false,
            lineStyle: {
              color: '#000',
              opacity: 1,
            },
            snap: true,
          },
          formatter(params) {
            const series = params
              .map(
                (param) => `<div>
                  <span style="display:inline-block;margin-right:4px;border-radius:10px;width:10px;height:10px;background-color:${
                    param.color
                  };"></span>
                  ${param.seriesName}
                </div>
                <b>${costsFormatter.format(param.data)}</b>`
              )
              .join('')

            const total = params.reduce((sum, param) => sum + param.data, 0)

            return `<div class="chart-tooltip">
              ${series}
              <div>Total</div>
              <b>${costsFormatter.format(total)}</b>
            </div>`
          },
          snap: true,
          transitionDuration: 0,
          trigger: 'axis',
        },

        xAxis: {
          boundaryGap: false,
          data: this.data.map((c) => c.period),
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
      const series = [
        {
          areaStyle: { opacity: 0.25 },
          data: this.data.map((c) => c.amount),
          emphasis: false,
          name: 'Repayment',
          showSymbol: false,
          stack: 'total',
          symbol: 'none',
          type: 'line',
        },
        {
          areaStyle: { opacity: 0.25 },
          data: this.data.map((c) => c.interest),
          emphasis: false,
          name: 'Interest',
          showSymbol: false,
          stack: 'total',
          symbol: 'none',
          type: 'line',
        },
      ]

      return {
        legend: series.map((s) => s.name),
        series,
      }
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
