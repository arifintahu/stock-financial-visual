<template>
  <el-table
    :data="data"
    stripe
    height="250"
    style="width: 100%">
    <el-table-column
      prop="Quarter"
      label="Quarter">
    </el-table-column>
    <el-table-column
      prop="Code"
      label="Code">
    </el-table-column>
    <el-table-column
      prop="PER"
      label="PER">
    </el-table-column>
    <el-table-column
      prop="PBV(%)"
      label="PBV(%)">
    </el-table-column>
    <el-table-column
      prop="EPS(Rp)"
      label="EPS(Rp)">
    </el-table-column>
    <el-table-column
      prop="BV(Rp)"
      label="BV(Rp)">
    </el-table-column>
    <el-table-column
      prop="ROE(%)"
      label="ROE(%)">
    </el-table-column>
    <el-table-column
      prop="NPM(%)"
      label="NPM(%)">
    </el-table-column>
  </el-table>
</template>

<script>
export default {
  name: 'Table',
  props: {
    data: Array
  },
  data() {
    return {
      
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
