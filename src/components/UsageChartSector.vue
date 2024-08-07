<template>
  <div>
    <div v-if="connectorUsageData && connectorUsageData.length > 0">
      <h2>Connector Usage Percentage</h2>
      <div class="scope-selector">
        <label for="scope-select">Select Scope (Days):</label>
        <select id="scope-select" v-model="localScope" @change="onScopeChange" class="scope-select">
          <option value="5">5</option>
          <option value="10">10</option>
          <option value="20">20</option>
          <option value="30">30</option>
        </select>
      </div>
      <div class="chart-container">
        <ConnectorUsageChart v-for="connector in connectorUsageData" :key="connector.connectorId"
          :connectorId="connector.connectorId" :usageData="connector.usageData" />
      </div>
    </div>
    <div v-else-if="connectorUsageData === null">Loading...</div>

    <div v-if="connectorTimePeriodData && connectorTimePeriodData.length > 0">
      <h2>Connector Usage Percentage by Time Period</h2>
      <div class="chart-container">
        <ConnectorTimePeriodChart v-for="connector in connectorTimePeriodData" :key="connector.connectorId"
          :connectorId="connector.connectorId" :usageData="connector.usageData" />
      </div>
    </div>
    <div v-else-if="connectorTimePeriodData === null">Loading Time Period Data...</div>
  </div>
</template>

<script>
import ConnectorUsageChart from '@/components/charts/ConnectorUsageChart.vue';
import ConnectorTimePeriodChart from '@/components/charts/ConnectorTimePeriodChart.vue';

export default {
  name: 'UsageChartSector',
  components: {
    ConnectorUsageChart,
    ConnectorTimePeriodChart,
  },
  props: {
    station: {
      type: Object,
      required: true,
    },
    connectorUsageData: {
      type: Array,
      default: () => [],
    },
    connectorTimePeriodData: {
      type: Array,
      default: () => [],
    },
    currentScope: {
      type: Number,
      default: 5,
    },
  },
  data() {
    return {
      // Local copy of the scope
      localScope: this.currentScope,
    };
  },
  watch: {
    // Watch for changes in the currentScope prop
    currentScope(newScope) {
      this.localScope = newScope;
    },
  },
  methods: {
    onScopeChange() {
      // Emit event to parent component to fetch new data based on selected scope
      this.$emit('fetch-connector-usage-data', parseInt(this.localScope));
    },
  },
};
</script>


<style scoped>
h2 {
  color: #277734;
  font-size: 24px;
  margin-bottom: 10px;
}

.scope-selector {
  display: flex;
  align-items: center;
  margin-bottom: 1rem;
}

.scope-selector label {
  margin-right: 0.5rem;
  font-weight: bold;
}

.scope-select {
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f9f9f9;
  font-size: 1rem;
  color: #333;
}

.scope-select:focus {
  outline: none;
  border-color: #2196f3;
  box-shadow: 0 0 5px rgba(33, 150, 243, 0.5);
}

.scope-select option {
  background-color: #fff;
  color: #333;
}

.chart-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.chart-container>* {
  flex: 0 0 auto;
  width: 490px;
  height: 350px;
  margin-right: 20px;
  margin-bottom: 20px;
}

.chart-container>*:last-child {
  margin-right: 0;
}
</style>