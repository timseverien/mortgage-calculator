<template>
  <div>Pizza!</div>
</template>

<script>
/* global echarts */

export default {
  data() {
    return {
      chartOptions: {
        title: {
          text: 'Hello',
        },
      },
    }
  },

  mounted() {
    if (!this.$isServer) {
      console.log(echarts)
    }
  },
}
</script>
