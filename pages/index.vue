<template>
  <b-container>
    <b-row class="section">
      <b-col>
        <h1>Mortgage calculator</h1>
      </b-col>
    </b-row>

    <b-row class="section">
      <b-col>
        <b-card-group deck>
          <ScenarioSettingsCard
            v-for="scenario in scenarios"
            v-bind="scenario"
            :key="scenario.key"
            :name="scenario.name"
            @change:amount="handleScenarioChange(scenario, 'amount', $event)"
            @change:fixedRatePeriod="
              handleScenarioChange(scenario, 'fixedRatePeriod', $event)
            "
            @change:interestRate="
              handleScenarioChange(scenario, 'interestRate', $event)
            "
            @change:interestRateAfterFixedRatePeriod="
              handleScenarioChange(
                scenario,
                'interestRateAfterFixedRatePeriod',
                $event
              )
            "
            @change:period="handleScenarioChange(scenario, 'period', $event)"
          />
        </b-card-group>
      </b-col>
    </b-row>

    <h2>What will it cost me?</h2>

    <b-row class="section">
      <b-col v-for="scenario in scenarios" :key="scenario.key">
        <CostsOverTimeChart
          :scenario="scenario"
          :data="getCostsOverTime(scenario)"
        />
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
export default {
  data() {
    return {
      scenarios: [
        this.createScenario(0),
        this.createScenario(1),
        this.createScenario(2),
      ],
    }
  },

  methods: {
    createScenario(index) {
      return {
        key: `scenario-${index}`,
        name: `Scenario ${index + 1}`,
        amount: 300000,
        fixedRatePeriod: 10 + index * 10,
        interestRate: 0.015,
        interestRateAfterFixedRatePeriod: 0.02,
        period: 30,
        type: 'linear',
      }
    },

    getAnnuityCostsOvertime(scenario) {
      return []
    },

    getCostsOverTime(scenario) {
      switch (scenario.type) {
        case 'annuity':
          return this.getAnnuityCostsOvertime(scenario)
        case 'linear':
          return this.getLinearCostsOvertime(scenario)
      }

      return null
    },

    getLinearCostsOvertime(scenario) {
      const amountPerYear = scenario.amount / scenario.period

      const costsPerYear = new Array(scenario.period)
        .fill()
        .map((_, yearIndex) => {
          const amount = amountPerYear
          const year = yearIndex

          const interestRate =
            yearIndex < scenario.fixedRatePeriod
              ? scenario.interestRate
              : scenario.interestRateAfterFixedRatePeriod

          const interest =
            (scenario.period - yearIndex) * amountPerYear * interestRate

          return { amount, interest, year }
        })

      return costsPerYear
    },

    handleScenarioChange(scenario, key, value) {
      scenario[key] = value
    },
  },
}
</script>

<style scoped>
.section {
  margin-bottom: 1rem;
}
</style>
