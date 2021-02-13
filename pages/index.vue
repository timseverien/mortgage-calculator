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
          />
        </b-card-group>
      </b-col>
    </b-row>

    <b-row class="section">
      <b-col>
        <CostsOverTimeChart />
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
        amount: 30000,
        fixedRatePeriod: 10,
        interestRate: 1.5,
      }
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
