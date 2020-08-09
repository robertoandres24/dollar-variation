<template>
  <div class="container">
    <div class="wrapper">
      <h1 class="title">
        usd-variation to CLP
      </h1>
      <line-chart v-if="loaded" :chartdata="chartdata" :options="options" />
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
          .map((clpDayInfo) => {
            return {
              value: parseInt(clpDayInfo.close),
              date: clpDayInfo.date,
            }
          })
          .map((curr, i, arr) => {
            if (i === 0) return 0
            return {
              value: curr.value - arr[i - 1].value,
              date: curr.date,
            }
          })
      )
    },
    fillChartData() {
      this.loaded = true
      const vm = this
      vm.chartdata = {
        labels: vm.variations.map((v) => vm.$moment(v.date)),
        datasets: [
          {
            label: 'Dollar variation',
            data: vm.variations.map((v) => v.value),
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
          xAxes: [
            {
              type: 'time',
              time: {
                unit: 'month',
              },
            },
          ],
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

<style>
.wrapper {
  padding-bottom: 5em;
}
</style>
