<template>
  <div class="container">
    <div>
      <h1 class="title">
        usd-variation
      </h1>
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
      variations: [],
    }
  },

  async created() {
    const { data: timeserie } = await this.$axios.get(
      `https://marketdata.tradermade.com/api/v1/timeseries?api_key=${this.apiKey}&currency=${this.currency}&start_date=${this.startDate}&end_date=${this.endDate}`
    )
    const values = timeserie.quotes.map((q) => parseInt(q.close))
    this.variations = values.map((curr, i, arr) => {
      if (i === 0) return 0
      return curr - arr[i - 1]
    })
  },
}
</script>

<style></style>
