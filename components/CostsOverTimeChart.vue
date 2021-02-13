<template>
  <div ref="container" class="chart-container"></div>
</template>

<script>
/* global echarts */

export default {
  data() {
    return {
      chart: null,

      chartOptions: {
        title: {
          text: 'ECharts entry example',
        },
        tooltip: {},
        legend: {
          data: ['Sales'],
        },
        xAxis: {
          data: [
            'shirt',
            'cardign',
            'chiffon shirt',
            'pants',
            'heels',
            'socks',
          ],
        },
        yAxis: {},
        series: [
          {
            name: 'Sales',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20],
          },
        ],
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
