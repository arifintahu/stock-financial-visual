<template>
  <div>
      <apexchart width="100%" type="line" :options="options" :series="series"></apexchart>
  </div>
</template>

<script>
export default {
  name: 'LineChart',
  props: {
    categories: Array,
    series: Array
  },
  data() {
    return {

    }
  },
  computed: {
    options() {
      return {
        colors: ['#003f5c', '#444e86b3', '#955196b3', '#dd5182b3', '#ff6e54b3', '#ffa600b3'],
        xaxis: {
          categories: this.categories
        }
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
