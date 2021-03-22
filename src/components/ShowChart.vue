<template>
  <el-card class="box-card">
    <template #header>
      <div class="card-header">
        <span>{{ data.title }}</span>
      </div>
    </template>
    <div class="card-body">
      <LineChart :categories="data.categories" :series="data.series" />
    </div>
  </el-card>
</template>

<script>
import LineChart from './LineChart.vue';

export default {
  name: 'ShowChart',
  props: {
    data: Object
  },
  components: {
    LineChart
  },
  data() {
    return {

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .card-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 20px;
      font-weight: 600;
  }
</style>
