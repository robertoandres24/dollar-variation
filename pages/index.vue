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
  async created() {
    const { data: todo } = await this.$axios.get(
      'https://jsonplaceholder.typicode.com/todos/1'
    )
    console.log(todo)
  },
}
</script>

<style></style>
