<template>
  <b-card bg-variant="dark" text-variant="white">
    <template #header>
      <h2 class="scenario-settings-card__header">{{ name }}</h2>
    </template>

    <b-form>
      <b-form-group :label-for="`scenario-${_uid}-type`" label="Type">
        <b-form-select v-model="typeModel" :options="typeOptions" />
      </b-form-group>
      <b-form-group
        :label-for="`scenario-${_uid}-fixed-rate-period`"
        label="Fixed-rate period"
      >
        <b-input-group append="years">
          <b-form-input
            :id="`scenario-${_uid}-fixed-rate-period`"
            v-model="fixedRatePeriodModel"
            max="30"
            min="0"
            type="number"
            step="1"
          />
        </b-input-group>
      </b-form-group>
      <b-form-group :label-for="`scenario-${_uid}-amount`" label="Amount">
        <b-input-group prepend="€">
          <b-form-input
            :id="`scenario-${_uid}-amount`"
            v-model="amountModel"
            min="1"
            step="1"
            type="number"
          />
        </b-input-group>
      </b-form-group>
      <b-form-group
        :label-for="`scenario-${_uid}-interest-rate`"
        label="Interest rate"
      >
        <b-input-group append="%">
          <b-form-input
            :id="`scenario-${_uid}-interest-rate`"
            v-model="interestRateModel"
            max="100"
            min="0"
            step="0.01"
            type="number"
          />
        </b-input-group>
      </b-form-group>
    </b-form>
  </b-card>
</template>

<script>
export default {
  props: {
    amount: {
      default: 0,
      type: Number,
    },

    fixedRatePeriod: {
      required: true,
      type: Number,
    },

    interestRate: {
      required: true,
      type: Number,
    },

    name: {
      required: true,
      type: String,
    },

    period: {
      required: true,
      type: Number,
    },

    type: {
      default: 'linear',
      type: String,
    },
  },

  data() {
    return {
      typeOptions: [
        { value: 'annuity', text: 'Annuity' },
        { value: 'linear', text: 'Linear' },
      ],
    }
  },

  computed: {
    amountModel: {
      get() {
        return this.amount.toString()
      },
      set(value) {
        this.$emit('change:amount', Number.parseInt(value))
      },
    },

    fixedRatePeriodModel: {
      get() {
        return this.fixedRatePeriod.toString()
      },
      set(value) {
        this.$emit('change:fixedRatePeriod', Number.parseInt(value))
      },
    },

    interestRateModel: {
      get() {
        return (100 * this.interestRate).toString()
      },
      set(value) {
        this.$emit('change:interestRate', Number.parseFloat(value) / 100)
      },
    },

    periodModel: {
      get() {
        return this.period.toString()
      },
      set(value) {
        this.$emit('change:period', Number.parseFloat(value))
      },
    },

    typeModel: {
      get() {
        return this.type.toString()
      },
      set(value) {
        this.$emit('change:type', value)
      },
    },
  },
}
</script>

<style scoped>
.scenario-settings-card__header {
  margin: 0;
  font-size: inherit;
  line-height: inherit;
}
</style>
