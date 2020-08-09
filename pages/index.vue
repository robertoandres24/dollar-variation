<template>
  <div class="container">
    <div>
      <h1 class="title">
        usd-variation
      </h1>
      <line-chart v-if="loaded" :chartdata="chartdata" :options="options" />
    </div>
    <div v-if="variations.length">
      <h3>Variations</h3>
      <p>{{ variations }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      apiKey: 'XcHFrKnvEQgMeSPsEjfR',
      currency: 'USDCLP',
      startDate: '2019-01-01',
      endDate: '2020-08-01',
      options: null,
      chartdata: null,
      variations: [],
      loaded: false,
    }
  },

  async created() {
    const { data: timeserie } = await this.$axios.get(
      `https://marketdata.tradermade.com/api/v1/timeseries?api_key=${this.apiKey}&currency=${this.currency}&start_date=${this.startDate}&end_date=${this.endDate}`
    )
    this.variations = await this.getVariations(timeserie.quotes)
    this.fillChartData()
  },

  methods: {
    getVariations(clpValuesInfo) {
      return Promise.all(
        clpValuesInfo
          .map((clpDayValue) => parseInt(clpDayValue.close))
          .map((curr, i, arr) => {
            if (i === 0) return 0
            return curr - arr[i - 1]
          })
      )
    },
    fillChartData() {
      this.loaded = true
      const vm = this
      vm.chartdata = {
        labels: vm.variations,
        datasets: [
          {
            label: 'Dollar variation',
            data: vm.variations,
          },
        ],
      }
      vm.options = {
        responsive: true,
        legend: {
          display: false,
        },
        title: {
          display: true,
          text: 'Dollar Variation',
        },
        scales: {
          yAxes: [
            {
              ticks: {
                beginAtZero: true,
              },
            },
          ],
        },
      }
    },
  },
}
</script>

<style></style>
