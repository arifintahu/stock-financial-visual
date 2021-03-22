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
        colors: ['#00587a', '#ef4f4f50'],
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
