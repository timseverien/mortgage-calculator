<template>
  <div class="page">
    <b-row class="section">
      <b-col>
        <h1>Mortgage calculator</h1>
      </b-col>
    </b-row>

    <b-row class="section">
      <b-col v-for="scenario in scenarios" :key="scenario.key" cols="12" lg="6">
        <ScenarioSettingsCard
          v-bind="scenario"
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
      </b-col>
    </b-row>

    <h2>What will it cost me?</h2>

    <b-row class="section">
      <b-col
        v-for="scenario in scenarioCosts"
        :key="scenario.key"
        cols="12"
        lg="6"
      >
        <CostsOverTimeChart
          :scenario="scenario"
          :data="scenario.costsOvertime"
        />

        <dl class="summary">
          <div>
            <dt>Total interest</dt>
            <dd>{{ scenario.totalInterestFormatted }}</dd>
          </div>

          <div>
            <dt>Total costs</dt>
            <dd>{{ scenario.totalFormatted }}</dd>
          </div>
        </dl>
      </b-col>
    </b-row>
  </div>
</template>

<script>
const periodsPerYear = 12

function createFormattedDateFromPeriod(period) {
  const currentYear = new Date().getFullYear()
  const year = (currentYear + Math.floor(period / 12))
    .toString()
    .padStart(4, '0')
  const month = ((period % 12) + 1).toString().padStart(2, '0')

  return `${year}-${month}`
}

export default {
  data() {
    return {
      scenarios: [this.createScenario(0), this.createScenario(1)],
    }
  },

  computed: {
    scenarioCosts() {
      const costsFormatter = new Intl.NumberFormat(undefined, {
        currency: 'EUR',
        maximumFractionDigits: 0,
        minimumFractionDigits: 0,
        style: 'currency',
      })

      return this.scenarios.map((scenario) => {
        const costsOvertime = this.getCostsOverTime(scenario)

        const total = costsOvertime.reduce(
          (sum, costs) => sum + costs.amount + costs.interest,
          0
        )

        const totalInterest = costsOvertime.reduce(
          (sum, costs) => sum + costs.interest,
          0
        )

        return {
          costsOvertime,
          totalFormatted: costsFormatter.format(total),
          totalInterestFormatted: costsFormatter.format(totalInterest),
        }
      })
    },
  },

  methods: {
    createScenario(index) {
      return {
        key: `scenario-${index}`,
        name: `Scenario ${index + 1}`,
        amount: 300000,
        fixedRatePeriod: 10,
        interestRate: 0.015,
        interestRateAfterFixedRatePeriod: 0.02,
        period: 30,
        type: index % 2 === 0 ? 'linear' : 'annuity',
      }
    },

    // https://www.investopedia.com/terms/p/present-value-annuity.asp
    // https://superuser.com/a/871411
    getAnnuityCostsOvertime(scenario) {
      const costsPerMonth = []
      const interestPeriods = periodsPerYear * scenario.period
      const interestRatePerMonth = scenario.interestRate / periodsPerYear

      const monthlyPayment =
        (scenario.amount * interestRatePerMonth) /
        (1 - Math.pow(1 + interestRatePerMonth, -interestPeriods))

      let debt = scenario.amount

      for (let i = 0; i < periodsPerYear * scenario.fixedRatePeriod; i++) {
        const interest = interestRatePerMonth * debt
        const amount = monthlyPayment - interest

        costsPerMonth.push({
          amount,
          interest,
          period: createFormattedDateFromPeriod(i),
        })

        debt -= amount
      }

      const interestPeriodsAfterFixedRatePeriod =
        periodsPerYear * (scenario.period - scenario.fixedRatePeriod)

      const interestRatePerMonthAfterFixedRatePeriod =
        scenario.interestRateAfterFixedRatePeriod / periodsPerYear

      const monthlyPaymentAfterFixedRatePeriod =
        (debt * interestRatePerMonthAfterFixedRatePeriod) /
        (1 -
          Math.pow(
            1 + interestRatePerMonthAfterFixedRatePeriod,
            -interestPeriodsAfterFixedRatePeriod
          ))

      for (
        let i = periodsPerYear * scenario.fixedRatePeriod;
        i < periodsPerYear * scenario.period;
        i++
      ) {
        const interest = interestRatePerMonthAfterFixedRatePeriod * debt
        const amount = monthlyPaymentAfterFixedRatePeriod - interest

        costsPerMonth.push({
          amount,
          interest,
          period: createFormattedDateFromPeriod(i),
        })

        debt -= amount
      }

      return costsPerMonth
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
      const costsPerMonth = []
      const amountPerYear = scenario.amount / scenario.period

      const amountPerMonth = amountPerYear / periodsPerYear

      for (let i = 0; i < periodsPerYear * scenario.period; i++) {
        const amount = amountPerMonth
        const year = Math.floor(i / periodsPerYear)

        const interestRate =
          (year < scenario.fixedRatePeriod
            ? scenario.interestRate
            : scenario.interestRateAfterFixedRatePeriod) / periodsPerYear

        const interest =
          (periodsPerYear * scenario.period - i) * amountPerMonth * interestRate

        costsPerMonth.push({
          amount,
          interest,
          period: createFormattedDateFromPeriod(i),
        })
      }

      return costsPerMonth
    },

    handleScenarioChange(scenario, key, value) {
      scenario[key] = value
    },
  },
}
</script>

<style scoped>
.page {
  padding: 1rem;
}

.section {
  margin-bottom: 1rem;
}

.summary {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(20ch, 1fr));
}
</style>
